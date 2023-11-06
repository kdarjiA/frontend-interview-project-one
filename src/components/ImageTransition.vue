<script setup>
import { Transition } from 'vue';
</script>
<template>
    <Transition name="slide-fade">
        <slot></slot>
    </Transition>
</template>

<style scoped>
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}
.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
</style>