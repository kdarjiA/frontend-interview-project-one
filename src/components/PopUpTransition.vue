<script setup>
import { Transition } from "vue";
</script>
<template>
  <Transition name="popup-fade" mode="out-in">
    <slot></slot>
  </Transition>
</template>

<style scoped>
.popup-fade-enter-active,
.popup-fade-leave-active {
  transition: opacity 0.5s;
}

.popup-fade-enter-from,
.popup-fade-leave-to {
  opacity: 0;
}
</style>
