<template>
  <div class="h-screen bg-gray-700">
    <header class="bg-gray-900 shadow" v-if="$route.meta.title">
      <div class="px-4 py-6 mx-auto max-w-7xl sm:px-6 lg:px-8">
        <h1
          @click="counter = 0"
          class="text-3xl font-bold leading-tight text-gray-400"
        >
          #{{ $route.meta.title }}
        </h1>
      </div>
    </header>
    <main>
      <router-view />
    </main>
  </div>
</template>
