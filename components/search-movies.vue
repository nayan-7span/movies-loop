<template>
  <div class="container">
    <div class="mb-8 flex flex-col xs:flex-row gap-2 sm:gap-4">
      <!-- // search by name, language  -->
      <input
        v-model="search"
        placeholder="Search movies by name, language..."
        class="bg-gray-700 xs:w-96 focus:outline-double outline-2 focus:outline-gray-500 outline-offset-2 text-white text-sm xs:text-base h-9 xs:h-12 rounded-lg px-4"
      />

      <div class="flex gap-2 sm:gap-4">
        <!-- // search by year  -->
        <input
          v-model="year"
          type="number"
          class="bg-gray-700 w-1/2 xs:w-32 md:w-40 no-spinner focus:outline-double outline-2 focus:outline-gray-500 outline-offset-2 text-white text-sm xs:text-base h-9 xs:h-12 rounded-lg px-4"
          placeholder="Year (e.g. 2023)"
        />

        <!-- // search by genre -->
        <div
          class="bg-gray-700 w-1/2 xs:w-32 md:w-40 text-white focus-within:outline-double outline-2 focus-within:outline-gray-500 outline-offset-2 text-sm xs:text-base h-9 xs:h-12 rounded-lg pr-4"
        >
          <select
            v-model="genreId"
            class="bg-gray-700 text-white mx-auto block focus:outline-none h-9 xs:h-12 rounded-lg px-2 sm:px-4"
          >
            <option :value="null">All Genres</option>
            <option v-for="genre in genres" :key="genre.id" :value="genre.id">
              {{ genre.name }}
            </option>
          </select>
        </div>
      </div>
      <button
        @click="fetchMovies"
        class="bg-danger-600 justify-center hover:bg-danger-700 px-5 flex items-center text-lg gap-2 font-semibold text-white py-2 rounded-lg"
      >
        <Icon name="heroicons:magnifying-glass-20-solid" class="text-2xl" />
        Search
      </button>
    </div>

    <div v-if="error" class="text-red-500">{{ error }}</div>
    <div
      v-if="loading"
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4 sm:gap-6"
    >
      <MovieCardShimmer v-for="item in 8" :key="item" />
    </div>
    <div
      v-else-if="movies.length"
      class="grid grid-cols-1 xs:grid-cols-2 md:grid-cols-4 gap-4 sm:gap-6"
    >
      <MovieCard v-for="movie in movies" :data="movie" :key="movie.id" />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const search = ref("");
const genreId = ref(null);
const year = ref(null);
const loading = ref(false);
const error = ref(null);
const movies = ref([]);

// Optional: Genre list (partial sample)
const genres = [
  { id: 28, name: "Action" },
  { id: 35, name: "Comedy" },
  { id: 18, name: "Drama" },
  { id: 27, name: "Horror" },
  { id: 10749, name: "Romance" },
];

const fetchMovies = async () => {
  loading.value = true;
  error.value = null;

  try {
    const baseUrl = search.value.trim()
      ? "https://api.themoviedb.org/3/search/movie"
      : "https://api.themoviedb.org/3/discover/movie";

    const params = new URLSearchParams({
      api_key: "41e7cfcdca28d4e6ade4c5f72bcca09f",
      language: "en-US",
      sort_by: "popularity.desc",
      include_adult: "false",
      ...(search.value && { query: search.value }),
      ...(year.value && { primary_release_year: year.value }),
      ...(genreId.value && { with_genres: genreId.value }),
    });

    const response = await fetch(`${baseUrl}?${params}`);
    const json = await response.json();

    if (json.results) {
      movies.value = json.results;
      console.log("the movies are", movies.value);
    } else {
      throw new Error(json.status_message || "Unknown error");
    }
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

// Initial load
fetchMovies();

useHead({
  title: "Search Movies | Movies Loop",
  meta: [
    {
      name: "description",
      content: "Search for movies by name, language, year, or genre.",
    },
  ],
});
</script>
