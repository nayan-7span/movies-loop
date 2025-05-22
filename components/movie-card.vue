<template>
  <NuxtLink
    :to="`/movie/${data.id}`"
    class="w-full relative group cursor-pointer hover:scale-105 transition-all border border-gray-700 duration-300 rounded-2xl overflow-hidden"
  >
    <button
      @click.prevent="handleFavorite(data)"
      class="absolute top-2 left-2 z-10 size-7 flex items-center justify-center bg-white rounded-full p-1"
    >
      <Icon
        v-if="isFavorite"
        name="material-symbols:favorite-rounded"
        class="text-xl text-red-500"
      />
      <Icon
        v-else
        name="material-symbols:favorite-outline-rounded"
        class="text-xl"
      />
    </button>
    <img
      v-if="data.poster_path"
      :src="`https://image.tmdb.org/t/p/w342${data.poster_path}`"
      :alt="data.title"
      class="w-full h-96 sm:h-fit object-cover object-top brightness-75 group-hover:brightness-50 transition-all duration-300"
    />

    <div
      v-else
      class="w-full h-96 sm:h-full flex items-center justify-center bg-gray-700"
    >
      <Icon
        name="fluent:movies-and-tv-20-regular"
        class="text-7xl text-gray-900"
      />
    </div>
    <div
      v-if="movieRating"
      class="absolute flex gap-1 rounded-full items-center font-bold right-2 px-2 py-0.5 shadow-lg top-2 bg-white"
    >
      <Icon
        name="material-symbols:star-rounded"
        class="text-xl text-yellow-400"
      />
      {{ movieRating }}
    </div>

    <div
      class="absolute xl:-mb-[100%] z-10 group-hover:-mb-0 transition-all duration-700 bottom-0 p-3 left-0 w-full bg-gray-900 bg-opacity-30 backdrop-blur-md rounded-t-2xl"
    >
      <div
        class="bg-gray-900 line-clamp-1 border border-gray-500 rounded-full text-sm w-fit text-white font-semibold py-1 px-3"
      >
        {{ data.title }}
      </div>
      <p class="text-xs line-clamp-5 mt-1 text-gray-300">{{ data.overview }}</p>
      <div
        class="text-xs flex justify-between border-t pt-1 border-gray-800 text-white mt-2"
      >
        <span v-if="data.release_date"> Year: {{ releaseYear }} </span>
        <span v-if="data.original_language"> Language: {{ language }} </span>
      </div>
    </div>
  </NuxtLink>
</template>
<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  data: {
    type: Object,
    required: true,
  },
});

const languageMap = {
  en: "English",
  es: "Spanish",
  fr: "French",
  de: "German",
  it: "Italian",
  ja: "Japanese",
  ko: "Korean",
  hi: "Hindi",
  zh: "Chinese",
  pt: "Portuguese",
  ru: "Russian",
  ar: "Arabic",
  tr: "Turkish",
  pl: "Polish",
  nl: "Dutch",
  sv: "Swedish",
  fi: "Finnish",
  da: "Danish",
  no: "Norwegian",
  he: "Hebrew",
  el: "Greek",
  ro: "Romanian",
  th: "Thai",
  id: "Indonesian",
  vi: "Vietnamese",
  uk: "Ukrainian",
  hu: "Hungarian",
  cs: "Czech",
  sk: "Slovak",
  fa: "Persian",
  ta: "Tamil",
  te: "Telugu",
  ml: "Malayalam",
  bn: "Bengali",
  ur: "Urdu",
};

const movieRating = computed(() => {
  return parseFloat(props.data.vote_average.toFixed(1));
});

const releaseYear = computed(() => {
  return props.data.release_date.split("-")[0];
});

const language = computed(() => {
  const code = props.data.original_language;
  return languageMap[code] || code?.toUpperCase() || "Unknown";
});

// --- FAVORITES FIX START ---
const favoriteMovies = ref([]);

onMounted(() => {
  const stored = JSON.parse(localStorage.getItem("favoriteMovies") || "[]");
  favoriteMovies.value = stored;
});

const isFavorite = computed(() => {
  return favoriteMovies.value.some((movie) => movie.id === props.data.id);
});

function handleFavorite(movie) {
  const index = favoriteMovies.value.findIndex((item) => item.id === movie.id);

  if (index !== -1) {
    favoriteMovies.value.splice(index, 1);
  } else {
    favoriteMovies.value.push(movie);
  }

  localStorage.setItem("favoriteMovies", JSON.stringify(favoriteMovies.value));
}
// --- FAVORITES FIX END ---
</script>
