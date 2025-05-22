<template>
  <Header />
  <div class="p-6 container">
    <div v-if="pending">Loading movie details...</div>
    <div v-else-if="error" class="text-red-500">Error: {{ error.message }}</div>

    <div v-else-if="movie" class="flex flex-col sm:flex-row gap-6 md:gap-8">
      <img
        v-if="movie.poster_path"
        :src="`https://image.tmdb.org/t/p/w342${movie.poster_path}`"
        :alt="movie.title"
        class="rounded-xl hidden md:block h-fit object-cover object-center shadow-md"
      />
      <img
        v-if="movie.poster_path"
        :src="`https://image.tmdb.org/t/p/w342${movie.poster_path}`"
        :alt="movie.title"
        class="rounded-xl block md:hidden h-fit object-cover object-center shadow-md"
      />
      <div>
        <!-- //top details  -->
        <h1 class="text-3xl font-bold text-white">{{ movie.title }}</h1>
        <h2 class="text-sm mt-1 font-medium text-white">{{ movie.tagline }}</h2>
        <div
          v-if="movie.vote_average"
          class="flex gap-1 mt-1 text-white items-center"
        >
          <Icon
            name="heroicons:star-20-solid"
            class="text-yellow-400 text-2xl"
          />
          <span>{{ movie.vote_average.toFixed(1) }}/10</span>
          <div class="text-gray-500 ml-2 flex items-center">
            (<Icon name="material-symbols:person" class="text-2xl" />
            {{ movie.vote_count }})
          </div>
        </div>

        <!-- // basic details  -->
        <p
          class="bg-warning-600 text-white my-4 py-0.5 px-4 font-semibold rounded-full w-fit"
        >
          {{ movie.status }}
        </p>
        <p class="text-gray-500 mb-2">
          <span class="text-warning-600 font-semibold">Released:</span>
          {{ movie.release_date }}
        </p>
        <p v-if="language" class="text-gray-500 mb-2">
          <span class="text-warning-600 font-semibold"> Language: </span>
          {{ language }}
        </p>
        <p v-if="movie.runtime" class="text-gray-500 mb-2">
          <span class="text-warning-600 font-semibold"> Runtime: </span>
          {{ movie.runtime }} minutes
        </p>
        <p v-if="movie.production_countries?.length" class="text-gray-500 mb-2">
          <span class="text-warning-600 font-semibold">
            Production Countries:
          </span>
          {{ countries }}
        </p>

        <!-- //description  -->
        <p class="mb-4 text-gray-400">{{ movie.overview }}</p>
        <div class="flex gap-2">
          <p
            v-for="(genre, index) in genres"
            :key="index"
            class="bg-primary-700 px-2 py-0.5 rounded text-sm text-white"
          >
            <span class=""> #{{ genre }} </span>
          </p>
        </div>
        <div
          v-if="produceBy || movie.homepage"
          class="text-gray-400 mt-5 flex-col md:flex-row gap-2 flex border-y border-gray-700 py-1 bg-gray-800 px-2 text-xs justify-between mb-2"
        >
          <p v-if="produceBy" class="w-full md:w-1/2">
            Produced by: {{ produceBy }}
          </p>
          <a
            :href="movie.homepage"
            target="_blank"
            class="hover:text-white whitespace-nowrap hover:underline"
            v-if="movie.homepage"
            >{{ movie.homepage }}</a
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useRoute } from "vue-router";
import { computed, watch } from "vue";
import { useHead } from "#imports";

const route = useRoute();
const movieId = route.params.id;

const {
  data: movie,
  pending,
  error,
} = await useFetch(`https://api.themoviedb.org/3/movie/${movieId}`, {
  query: {
    api_key: "41e7cfcdca28d4e6ade4c5f72bcca09f",
    language: "en-US",
  },
  key: `movie-${movieId}`,
  server: true,
  lazy: true,
  default: () => ({}),
});

const genres = computed(() => {
  return movie.value.genres
    ? movie.value.genres.map((genre) => genre.name)
    : null;
});

const countries = computed(() => {
  return movie.value.production_countries
    ? movie.value.production_countries.map((country) => country.name).join(", ")
    : null;
});

const language = computed(() => {
  return movie.value.spoken_languages
    ? movie.value.spoken_languages.map((lang) => lang.english_name).join(", ")
    : null;
});

const produceBy = computed(() => {
  return movie.value.production_companies
    ? movie.value.production_companies.map((company) => company.name).join(", ")
    : null;
});

watch(movie, (val) => {
  if (!val) return;

  useHead({
    title: val.title,
    meta: [
      { name: "description", content: val.overview },
      { property: "og:title", content: val.title },
      { property: "og:description", content: val.overview },
      {
        property: "og:image",
        content: `https://image.tmdb.org/t/p/w500${val.poster_path}`,
      },
      { property: "og:type", content: "video.movie" },
      { property: "og:release_date", content: val.release_date },
      { name: "twitter:card", content: "summary_large_image" },
      { name: "twitter:title", content: val.title },
      { name: "twitter:description", content: val.overview },
      {
        name: "twitter:image",
        content: `https://image.tmdb.org/t/p/w500${val.poster_path}`,
      },
    ],
    link: [
      {
        rel: "canonical",
        href: `https://your-domain.com/movie/${movieId}`,
      },
    ],
  });
});
</script>
