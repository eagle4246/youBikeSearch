<script setup>
import { onMounted, ref, computed, watch, reactive } from 'vue';
import YoubikeTitle from './components/YoubikeTitle.vue';
import EmptySearch from './components/EmptySearch.vue';
import OtherFeatures from './components/OtherFeatures.vue';
import axios from 'axios';
import CardList from './components/CardList.vue';

const stationData = ref([]);
const inputSearchTerm = ref('');
const confirmedSearchTerm = ref('');
const showDislike = ref(true)

const stationAreas = ref()
const stationsAreasEn = ref();

const showFavorites = ref(false)
const showBlockedList = ref(false)
const targetArea = ref('')

const storedState = JSON.parse(localStorage.getItem('stationPreferences') || '{}')

const stationPreferences = reactive({
  favoriteMap: storedState.favoriteMap || {},
  dislikeMap: storedState.dislikeMap || {}
})

const filteredStations = computed(() => {
  const lower = confirmedSearchTerm.value?.toLowerCase() || ''

  // Step 1: Apply search filtering
  const filtered = stationData.value.filter(station => {
    if (!lower) return true
    return (
      station.sna.toLowerCase().includes(lower) ||
      station.snaen.toLowerCase().includes(lower) ||
      station.sarea.toLowerCase().includes(lower) ||
      station.sareaen.toLowerCase().includes(lower) ||
      station.ar.toLowerCase().includes(lower) ||
      station.aren.toLowerCase().includes(lower)
    )
  })

  // Step 2: Add favorite/dislike states
  const enriched = filtered.map(station => ({
    ...station,
    favorite: !!stationPreferences.favoriteMap[station.sno],
    dislike: !!stationPreferences.dislikeMap[station.sno]
  }))

  // Step 3a: Apply "Show Only Favorites" filter
  const visible = refinedFilter(enriched)

  // Step 4: Sort — favorites first, then normal, then dislikes (if shown)
  return visible.sort((a, b) => {
    // sort value: favorite (-2) < normal (0) < dislike (2)
    const sortValue = (s) => {
      if (s.favorite) return -2
      if (s.dislike) return 2
      return 0
    }
    return sortValue(a) - sortValue(b)
  })
})

const refinedFilter = (stations) => {

  if (targetArea.value) {
    return stations.filter(station => station.sarea === targetArea.value)
  }
  if (showFavorites.value) {
    return stations.filter(station => station.favorite);
  }
  // NEW: Step 3b: Apply "Show Only Dislikes" filter
  else if (showBlockedList.value) {
    return stations.filter(station => station.dislike);
  }
  // Existing: Step 3c: Filter out dislikes if showDislike is false (only if not showing only favorites/dislikes)
  else {
    return stations.filter(station => {
      if (station.dislike && !showDislike.value) return false
      return true
    })
  }
}

// Function to trigger the search
const performSearch = () => {
  confirmedSearchTerm.value = inputSearchTerm.value;
  inputSearchTerm.value = '';
  showFavorites.value = false;
  showBlockedList.value = false;
  targetArea.value = '';
};

// Function to clear the search term and results
const clearSearch = () => {
  inputSearchTerm.value = '';
  confirmedSearchTerm.value = ''; // Clear the confirmed search to show all results
  showFavorites.value = false;
  showBlockedList.value = false;
  targetArea.value = '';
};


const toggleFavorite = (sno) => {
  stationPreferences.favoriteMap[sno] = !stationPreferences.favoriteMap[sno]
   // If the station is now favorited, ensure it's not disliked
  if (stationPreferences.favoriteMap[sno]) {
    stationPreferences.dislikeMap[sno] = false;
  }
}

const toggleDislike = (sno) => {
  stationPreferences.dislikeMap[sno] = !stationPreferences.dislikeMap[sno]

  // If the station is now disliked, ensure it's not favorited
  if (stationPreferences.dislikeMap[sno]) {
    stationPreferences.favoriteMap[sno] = false;
  }
};

const showOnlyFavorites = () => {
  showFavorites.value = true;
  if (showFavorites.value) {
    showBlockedList.value = false;
    confirmedSearchTerm.value = ''; // Clear search when activating this filter
    inputSearchTerm.value = '';
    targetArea.value = '';
  }
}

const showOnlyBlockedList = () => {
  showBlockedList.value = true;
  if (showBlockedList.value) {
    showFavorites.value = false;
    confirmedSearchTerm.value = ''; // Clear search when activating this filter
    inputSearchTerm.value = '';
    targetArea.value = '';
  }
}

const showArea = (area) => {
  targetArea.value = area;
  showFavorites.value = false;
  showBlockedList.value = false;
  inputSearchTerm.value = ''
}


const clearAllFavorites = () => {
  if (confirm("Are you sure you want to remove all favorites?")) {
    Object.keys(stationPreferences.favoriteMap).forEach(key => {
      delete stationPreferences.favoriteMap[key]
    })
  }
}

const getData = async () => {
  const { data } = await axios.get('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  stationData.value = data
  const { chinese, english } = getUniqueAreas(stationData.value)
  stationAreas.value = chinese;
  stationsAreasEn.value = english
}

const getUniqueAreas = (data) => {
  const chineseAreas = new Set();
  const englishAreas = new Set();

  data.forEach(station => {
    if (station.sarea) {
      chineseAreas.add(station.sarea);
    }
    if (station.sareaen) {
      englishAreas.add(station.sareaen);
    }
  });

  return {
    chinese: Array.from(chineseAreas).sort(), // Convert Set to Array and sort alphabetically
    english: Array.from(englishAreas).sort()  // Convert Set to Array and sort alphabetically
  };
}
onMounted(() => {
  getData();
})

watch(
  () => ({ ...stationPreferences.favoriteMap, ...stationPreferences.dislikeMap }),
  () => {
    localStorage.setItem('stationPreferences', JSON.stringify({
      favoriteMap: stationPreferences.favoriteMap,
      dislikeMap: stationPreferences.dislikeMap
    }))
  },
  { deep: true }
)

</script>
<template>
  <main>
    <section class="p-4 pb-0 bg-base-100">
      <div class="navbar flex justify-center">
        <YoubikeTitle class="flex flex-col sm:flex-row items-baseline just gap-3 ml-auto" />
        <OtherFeatures class="ml-auto self-start sm:self-center" v-model="showDislike" @clearAllFavorites="clearAllFavorites"/>
      </div>
      <div class='flex flex-col gap-8 p-4 justify-center'>
        <div class="flex flex-col sm:flex-row gap-3 justify-center">
          <label class="input input-primary w-full sm:w-1/2 lg:w-1/3">
            <svg class="h-[1em] opacity-50" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
              <g stroke-linejoin="round" stroke-linecap="round" stroke-width="2.5" fill="none" stroke="currentColor">
                <circle cx="11" cy="11" r="8"></circle>
                <path d="m21 21-4.3-4.3"></path>
              </g>
            </svg>
            <input type="search" required placeholder="Search by station name, area, or address..."
              v-model="inputSearchTerm" @keyup.enter="performSearch" />
          </label>
          <button class="btn btn-primary" @click="performSearch">Search</button>
          <button class="btn btn-error" @click="clearSearch">Clear</button>
        </div>
        <div class="flex flex-wrap gap-3">
          <button class="badge badge-info hover:badge cursor-pointer" :class="showFavorites ? '' : 'badge-outline'"
            @click="showOnlyFavorites">Favorites</button>
          <button class="badge badge-accent hover:badge-soft cursor-pointer"
            :class="showBlockedList ? '' : 'badge-outline'" @click="showOnlyBlockedList">Block List</button>
          <button v-for="(area, index) in stationAreas" :key=index
            class="badge badge-outline badge-success hover:badge-soft cursor-pointer"
            :class="area == targetArea ? 'badge-warning' : 'badge-success'" @click="showArea(area)">{{ area }}</button>
        </div>
      </div>
    </section>
    <div class="divider"></div>
    <div v-if="filteredStations.length > 0" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3 items-start">
      <CardList :filteredStations="filteredStations" @setFavorite="toggleFavorite" @setDislike="toggleDislike"/>
    </div>
    <div v-else>
      <EmptySearch />
    </div>
  </main>
</template>
<style scoped></style>
