<script setup>
defineProps({
  filteredStations: {
    type: Array
  }
})

const emits = defineEmits(['setFavorite', 'setDislike'])

// Function to format the date and time
const formatDateTime = (dateTimeString) => {
  const options = { year: 'numeric', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit' };
  return new Date(dateTimeString).toLocaleDateString(undefined, options);
};

const setFavorite = (sno) => {
  emits('setFavorite', sno)
}

const setDislike = (sno) => {
  emits('setDislike', sno)
}

const dislikedText = (dislike) => {
  return dislike ? 'Removed' : 'Remove'
}

const cleanStationName = (name) => {
      return name.replace('YouBike2.0_', '');
    }

</script>
<template>
  <div v-for="station in filteredStations" :key="station.sno"
    class="collapse collapse-arrow bg-base-100 border-base-300 border">
    <input type="checkbox" />
    <div class="collapse-title font-semibold flex items-center justify-between gap-3">
      <span>
        {{ cleanStationName(station.sna) }}
      </span>
      <svg xmlns="http://www.w3.org/2000/svg" :class="station.favorite ? 'fill-error' : 'fill-none'" viewBox="0 0 24 24"
        stroke-width="2.5" stroke="currentColor" class="size-[1.2em]">
        <path stroke-linecap="round" stroke-linejoin="round"
          d="M21 8.25c0-2.485-2.099-4.5-4.688-4.5-1.935 0-3.597 1.126-4.312 2.733-.715-1.607-2.377-2.733-4.313-2.733C5.1 3.75 3 5.765 3 8.25c0 7.22 9 12 9 12s9-4.78 9-12Z" />
      </svg>
    </div>
    <div class="collapse-content text-sm">
      <div class="divider m-0"></div>
      <p><strong>Name:</strong> {{ cleanStationName(station.sna) }} <i>({{ cleanStationName(station.snaen) }})</i></p>
      <p><strong>Area:</strong> {{ station.sarea }} <i>({{ station.sareaen }})</i></p>
      <p><strong>Address:</strong> {{ station.ar }} <i>({{ station.aren }})</i></p>
      <p><strong>Available Bikes:</strong> {{ station.available_rent_bikes }}</p>
      <p><strong>Quantity:</strong> {{ station.Quantity }}</p>
      <p class="my-3 text-neutral-700 italic">Last Updated: {{ formatDateTime(station.updateTime) }}</p>
      <div class="grid grid-cols-2 gap-3">
        <button class="btn btn-outline" @click.stop="setFavorite(station.sno)">
          <svg xmlns="http://www.w3.org/2000/svg" :class="station.favorite ? 'fill-error' : 'fill-none'"
            viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor" class="size-[1.2em]">
            <path stroke-linecap="round" stroke-linejoin="round"
              d="M21 8.25c0-2.485-2.099-4.5-4.688-4.5-1.935 0-3.597 1.126-4.312 2.733-.715-1.607-2.377-2.733-4.313-2.733C5.1 3.75 3 5.765 3 8.25c0 7.22 9 12 9 12s9-4.78 9-12Z" />
          </svg>
          Like
        </button>
        <button class="btn btn-dash flex items-center" @click.stop="setDislike(station.sno)">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
          <span>{{ dislikedText(station.dislike) }}</span>
        </button>
      </div>
    </div>
  </div>
</template>