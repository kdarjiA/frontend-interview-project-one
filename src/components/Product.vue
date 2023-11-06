<script setup>
import ProductDetail from "./ProductDetail.vue";
import ProductSlider from "./ProductSlider.vue";
</script>

<template>
  <section class="text-gray-600 body-font overflow-hidden">
    <div class="container px-5 py-24 mx-auto">
      <div class="lg:w-4/5 mx-auto flex flex-wrap">
        <ProductSlider></ProductSlider>
        <ProductDetail></ProductDetail>
      </div>
    </div>
  </section>
</template>

<style></style>
