<script setup>
import { ref } from "vue";

const images = [
  `/src/assets/product.jpeg`,
  `/src/assets/front.jpeg`,
  `/src/assets/back.jpeg`,
];
const activeImage = ref(0);
const zoomImage = ref(true);

const changeImage = (image) => {
  activeImage.value = image;
};

const nextImage = () => {
  if (activeImage.value === images.length - 1) return;
  activeImage.value++;
};

const prevImage = () => {
  if (activeImage.value === 0) return;
  activeImage.value--;
};

const zoomIn = (event) => {
  if (zoomImage.value) {
      var element = document.getElementById("overlay");
      element.style.display = "inline-block";
          element.style.backgroundImage = `url(${window.location.origin+images[activeImage.value]})`;
    var img = document.getElementById("main-image");
    var posX = event.offsetX ? event.offsetX : event.pageX + img.offsetLeft;
    var posY = event.offsetY ? event.offsetY : event.pageY + img.offsetTop;
    element.style.backgroundPosition =
      -posX + 250 + "px " + (-posY + 100) + "px";
  }
};

const zoomOut = () => {
  var element = document.getElementById("overlay");
  element.style.display = "none";
};
</script>

<template>
  <div class="flex flex-col-reverse">
    <!-- Image selector -->
    <div class="mx-auto mt-6 hidden w-full max-w-2xl sm:block lg:max-w-none">
      <div
        class="grid grid-cols-4 gap-6"
        aria-orientation="horizontal"
        role="tablist"
      >
        <button
          class="relative flex h-24 cursor-pointer items-center justify-center rounded-md bg-white text-sm font-medium uppercase text-gray-900 hover:bg-gray-50 focus:outline-none focus:ring focus:ring-opacity-50 focus:ring-offset-4"
          aria-controls="tabs-1-panel-1"
          role="tab"
          type="button"
          v-for="(image, key) in images"
          @click="changeImage(key)"
        >
          <span class="sr-only">Angled view</span>
          <span class="absolute inset-0 overflow-hidden rounded-md">
            <img
              :src="image"
              alt=""
              class="h-full w-full object-cover object-center"
            />
          </span>
          <!-- Selected: "ring-indigo-500", Not Selected: "ring-transparent" -->
          <span
            class="ring-transparent pointer-events-none absolute inset-0 rounded-md ring-2 ring-offset-2"
            aria-hidden="true"
          ></span>
        </button>
        <!-- More images... -->
      </div>
    </div>

    <div class="aspect-h-1 aspect-w-1 w-full relative">
      <div
        class="absolute inset-0 flex items-center justify-center pointer-events-none"
        id="main-image-container"
      >
        <!-- Arrows to navigate between images -->
        <button
          class="absolute left-0 top-1/2 transform -translate-y-1/2 bg-white rounded-full p-2 cursor-pointer pointer-events-auto"
          @click="prevImage"
        >
          &lt;
          <!-- Left arrow icon or text -->
        </button>
        <button
          class="absolute right-0 top-1/2 transform -translate-y-1/2 bg-white rounded-full p-2 cursor-pointer pointer-events-auto"
          @click="nextImage"
        >
          &gt;
          <!-- Right arrow icon or text -->
        </button>
      </div>

      <!-- Tab panel, show/hide based on tab state. -->
      <div
        aria-labelledby="tabs-1-tab-1"
        role="tabpanel"
        tabindex="0"
        class="aspect-square w-full h-full"
      >
        <img
          :src="images[activeImage]"
          alt="Angled front view with bag zipped and handles upright."
          class="object-center object-contain aspect-square"
          id="main-image"
          @mousemove="zoomIn"
          @mouseout="zoomOut"
        />
        <div
          id="overlay"
          class="absolute top-0 right-0 left-full bg-white z-50"
        ></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
#overlay {
  border: 1px solid black;
  width: 500px;
  height: 500px;
  display: inline-block;
  /* background-image: url("http://localhost:5173/src/assets/product.jpeg"); */
  background-repeat: no-repeat;
}
</style>
