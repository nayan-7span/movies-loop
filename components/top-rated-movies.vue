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
// useFetch with caching
const {
  data: movies,
  pending,
  error,
} = await useFetch("https://api.themoviedb.org/3/movie/popular", {
  query: {
    api_key: "41e7cfcdca28d4e6ade4c5f72bcca09f",
    language: "en-US",
    page: 1,
  },
  // Enables caching in SSR context
  key: "popular-movies", // Unique cache key
  server: true, // Runs only on server (SSR)
  lazy: true, // Lazy fetch (on-demand instead of on server render)
  default: () => ({ results: [] }), // default structure to avoid null errors
});

const topMovies = computed(() => {
  if (movies.value?.results) {
    return [...movies.value.results]
      .sort((a, b) => b.vote_average - a.vote_average)
      .slice(0, 8);
  }
  return [];
});
</script>
