<template>
  <div class="container">
    <h1 class="mb-4 sm:mb-10 text-white font-bold text-2xl sm:text-5xl">
      Latest Top Rated Movies
    </h1>
    <div
      v-if="topMovies?.length"
      class="grid grid-cols-1 xs:grid-cols-2 md:grid-cols-4 gap-4 sm:gap-6"
    >
      <MovieCard v-for="movie in topMovies" :data="movie" :key="movie.id" />
    </div>
  </div>
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
