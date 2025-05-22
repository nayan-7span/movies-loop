<template>
  <NuxtLoadingIndicator />
  <nav
    class="py-3 sm:py-4 w-full sticky top-0 bg-gray-900 z-50 border-b border-gray-700"
  >
    <div class="flex container justify-between w-full">
      <nuxt-link to="/" class="font-bold flex gap-2 items-center">
        <Icon
          name="fluent:movies-and-tv-20-regular"
          class="text-4xl sm:text-5xl text-danger-500"
        />
        <div
          class="border-l-2 pl-2 sm:pl-3 text-xs sm:text-base border-white -ml-1"
        >
          <p class="text-white leading-tight">
            Movies <br />
            Loop
          </p>
        </div>
      </nuxt-link>
    </div>
  </nav>
</template>
