<script setup>
import Logo from "../assets/logo.webp"
</script>
<template>
  <header class="text-gray-600 body-font">
    <div
      class="container mx-auto flex flex-wrap p-5 flex-col md:flex-row items-center"
    >
      <a
        class="flex title-font font-medium items-center text-gray-900 mb-4 md:mb-0"
      >
        <img :src="Logo" />
      </a>
    </div>
  </header>
</template>
