<script setup>
  const showDislike = defineModel();
  const emits = defineEmits(['clearAllFavorites'])
  const resetFavorites = () => {
    emits('clearAllFavorites')
  }
</script>
<template>
  <div class="ml-auto self-start sm:self-center">
    <div class="dropdown dropdown-end">
      <div tabindex="0" role="button" class="btn btn-square btn-ghost">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
          class="inline-block h-5 w-5 stroke-current">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M5 12h.01M12 12h.01M19 12h.01M6 12a1 1 0 11-2 0 1 1 0 012 0zm7 0a1 1 0 11-2 0 1 1 0 012 0zm7 0a1 1 0 11-2 0 1 1 0 012 0z">
          </path>
        </svg>
      </div>
      <ul tabindex="0" class="dropdown-content menu bg-base-100 rounded-box z-1 w-52 p-2 shadow-sm">
        <li>
          <label class="label text-sm">
            <input type="checkbox" v-model="showDislike" class="checkbox checkbox-xs" />
            Show Dislike
          </label>
        </li>
        <li>
          <button @click="resetFavorites"
            class="btn btn-sm btn-outline text-red-500 border-red-300 hover:bg-red-100">
            Clear All Favorites
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>