<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <main class="h-screen bg-slate-100 text-slate-800">
    <div class="h-10pc flex justify-center items-center">
      <div class="inline-flex rounded-md shadow-sm" role="group">
        <RouterLink to="/">
          <button
            type="button"
            class="inline-flex items-center justify-center w-40 py-2 px-4 text-sm font-medium text-gray-900 bg-transparent rounded-l-lg border border-gray-900 hover:bg-gray-900 hover:text-white focus:z-10 focus:bg-gray-900 focus:text-white"
          >
            <svg
              class="mr-1 w-4 h-4 fill-cyan-500"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd" 
                d="M10 18a8 8 0 100-16 8 8 0 000 16zM7 9a1 1 0 100-2 1 1 0 000 2zm7-1a1 1 0 11-2 0 1 1 0 012 0zm-.464 5.535a1 1 0 10-1.415-1.414 3 3 0 01-4.242 0 1 1 0 00-1.415 1.414 5 5 0 007.072 0z"
                clip-rule="evenodd"
              ></path>
            </svg>
            Recognition
          </button></RouterLink
        >
        <RouterLink to="/profile"
          ><button
            type="button"
            class="inline-flex items-center justify-center w-40 py-2 px-4 text-sm font-medium text-gray-900 bg-transparent rounded-r-md border border-gray-900 hover:bg-gray-900 hover:text-white focus:z-10 focus:bg-gray-900 focus:text-white"
          >
            <svg
              class="mr-1 w-4 h-4 fill-cyan-500"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M3 12v3c0 1.657 3.134 3 7 3s7-1.343 7-3v-3c0 1.657-3.134 3-7 3s-7-1.343-7-3z"
              ></path>
              <path
                d="M3 7v3c0 1.657 3.134 3 7 3s7-1.343 7-3V7c0 1.657-3.134 3-7 3S3 8.657 3 7z"
              ></path>
              <path
                d="M17 5c0 1.657-3.134 3-7 3S3 6.657 3 5s3.134-3 7-3 7 1.343 7 3z"
              ></path>
            </svg>
            Profiles
          </button></RouterLink
        >
      </div>
    </div>
    <RouterView class="h-90pc" />
  </main>
</template>

<style>
@import "@/assets/base.css";
</style>
