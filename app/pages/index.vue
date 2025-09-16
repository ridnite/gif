<template>
  <div class="w-full h-full flex flex-col items-center justify-center text-neutral-100">
    <div class="input-container" :class="{'input-active': inputText.length > 0}">
      <input
        type="text"
        class="p-4 w-full h-14 bg-slate-700 text-lg rounded-full focus:outline-none focus:ring-2 focus:ring-purple-500 transition-colors duration-300"
        placeholder="Harika bir GIF ara..."
        v-model="inputText"
      />
      
      <div
        class="search-results-box bg-slate-800 mt-4 p-4 rounded-xl shadow-lg grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4 overflow-y-auto max-h-[70vh]"
        v-if="inputText.length > 0 && !pending"
      >
        <div v-for="gif in gifs?.data" :key="gif.id" class="gif-item rounded-lg overflow-hidden">
          <img :src="gif.images.fixed_height.url" :alt="gif.title" class="w-full h-full object-cover">
        </div>
      </div>
      
      <div v-if="pending" class="text-white mt-4">GIF'ler yükleniyor...</div>
      
      <div v-if="inputText.length > 0 && gifs?.data.length === 0 && !pending" class="text-white mt-4">
        Sonuç bulunamadı.
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';

const inputText = ref('');
const apiKey = "APİ ANAHTARI";

const gifs = ref(null);
const pending = ref(false);

const searchGifs = async (query) => {
  if (query.length === 0) {
    gifs.value = null;
    return;
  }
  
  pending.value = true;
  try {
    const response = await $fetch(`https://api.giphy.com/v1/gifs/search`, {
      params: {
        api_key: apiKey,
        q: query,
        limit: 24 
      }
    });
    gifs.value = response;
  } catch (error) {
    console.error("API'den veri çekme hatası:", error);
    gifs.value = { data: [] }; 
  } finally {
    pending.value = false;
  }
};

watch(inputText, (newVal) => {
  searchGifs(newVal);
});
</script>

<style scoped>
.input-container {
  width: 100%;
  max-width: 50rem;
  transition: transform 0.5s ease-out, margin-top 0.5s ease-out;
}
.input-active {
  transform: translateY(-5vh);
}
.search-results-box {
  transition: opacity 0.5s ease-in-out;
}
.gif-item {
  aspect-ratio: 1 / 1; 
}
.search-results-box {
    -ms-overflow-style: none; 
    scrollbar-width: none; 
}
.search-results-box::-webkit-scrollbar {
    display: none;
}
</style>