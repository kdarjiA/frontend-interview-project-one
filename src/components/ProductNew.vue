<template>
  <div class="bg-gray-200">
    <div
      class="mx-auto max-w-2xl px-4 py-16 sm:px-6 sm:py-24 lg:max-w-7xl lg:px-8"
    >
      <div class="lg:grid lg:grid-cols-2 lg:items-start lg:gap-x-8">
        <!-- Image gallery -->
        <ProductImageSlider></ProductImageSlider>
        <!-- Product info -->
        <ProductDetail></ProductDetail>
      </div>
    </div>
  </div>
</template>

<script setup>
import ProductDetail from "./ProductDetail.vue";
import ProductImageSlider from "./ProductSlider.vue"
</script>
