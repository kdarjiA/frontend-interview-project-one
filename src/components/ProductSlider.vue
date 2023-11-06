<script setup>
import { onMounted, ref } from "vue";
import PopUpViewer from "./PopUpViewer.vue";
import ImageTransition from "./ImageTransition.vue";
import PopUpTransition from "./PopUpTransition.vue";

const images = {
  0: {
    thumb: `/src/assets/product-thumb.jpeg`,
    image: `/src/assets/product.jpeg`,
    large: `/src/assets/product-large.jpeg`,
    type: "image",
  },
  1: {
    thumb: `/src/assets/front-thumb.jpeg`,
    image: `/src/assets/front.jpeg`,
    large: `/src/assets/front-large.jpeg`,
    type: "image",
  },
  2: {
    thumb: `/src/assets/back-thumb.jpeg`,
    image: `/src/assets/back.jpeg`,
    large: `/src/assets/back-large.jpeg`,
    type: "image",
  },
  3: {
    video: `/src/assets/product-video.mp4`,
    type: "video",
  },
  4: {
    video: `/src/assets/product-video-two.mp4`,
    type: "video",
  },
};
const activeImage = ref(0);
const showPopupFlag = ref(false);
const touchStartX = ref(0);
const touchEndX = ref(0);

const changeImage = (image) => {
  activeImage.value = image;
};

const nextImage = () => {
  if (activeImage.value === Object.keys(images).length - 1) {
    activeImage.value = 0;
    return;
  }
  activeImage.value++;
};

const prevImage = () => {
  if (activeImage.value === 0) {
    activeImage.value = Object.keys(images).length - 1;
    return;
  }
  activeImage.value--;
};

const zoomIn = (e) => {
  if (screen.width > 760) {
    var element = document.getElementById("overlay");
    element.style.display = "inline-block";
    element.style.backgroundImage = `url(${
      window.location.origin + images[activeImage.value].image
    })`;
    // var img = document.getElementById("main-image");
    // var posX = event.offsetX ? event.offsetX : event.pageX + img.offsetLeft;
    // var posY = event.offsetY ? event.offsetY : event.pageY + img.offsetTop;
    // element.style.backgroundPosition = -posX + 250 + "px " + (-posY + 100) + "px";

    let clientX = e.clientX - element.offsetLeft;
    let clientY = e.clientY - element.offsetTop;

    let mWidth = element.offsetWidth;
    let mHeight = element.offsetHeight;

    clientX = (clientX / mWidth) * 100;
    clientY = (clientY / mHeight) * 100;

    element.style.backgroundPosition = clientX + "% " + clientY + "%";
  }
};

const zoomOut = () => {
  var element = document.getElementById("overlay");
  element.style.display = "none";
};

const showPopup = () => {
  showPopupFlag.value = true;
};

const closePopup = () => {
  showPopupFlag.value = false;
};

const handleTouchStart = (e) => {
  touchStartX.value = e.touches[0].clientX;
};

const handleTouchEnd = (e) => {
  touchEndX.value = e.changedTouches[0].clientX;
  handleSwipe();
};

const handleSwipe = () => {
  const threshold = 50; // Adjust this threshold as needed
  const deltaX = touchEndX.value - touchStartX.value;

  if (deltaX > threshold) {
    // Swipe left, change to the previous image
    prevImage();
  } else if (deltaX < -threshold) {
    // Swipe right, change to the next image
    nextImage();
  }
};

onMounted(() => {});
</script>

<template>
  <div class="flex flex-col-reverse bg-white p-8">
    <!-- Image selector -->
    <div class="mx-auto mt-6 hidden w-full max-w-2xl sm:block lg:max-w-none">
      <div
        class="grid grid-cols-4 gap-6"
        aria-orientation="horizontal"
        role="tablist"
      >
        <button
          class="relative flex h-20 cursor-pointer items-center justify-center rounded-md bg-white text-sm font-medium uppercase text-gray-900 hover:bg-gray-50 focus:outline-none"
          aria-controls="tabs-1-panel-1"
          role="tab"
          type="button"
          v-for="(image, key) in images"
          @click="changeImage(key)"
          :class="{ 'ring ring-yellow-500 ring-4': key == activeImage }"
        >
          <span class="sr-only">Angled view</span>
          <span class="absolute inset-0 overflow-hidden rounded-md">
            <img
              :src="image.thumb"
              :alt="key"
              class="object-center object-contain aspect-square w-full h-full"
              v-if="image.type === 'image'"
            />
            <div class="relative" v-else>
              <video :src="image.video"></video>
              <div class="absolute inset-0 flex items-center justify-center">
                <button class="text-2xl text-white">▶️</button>
              </div>
            </div>
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
          class="bg-white absolute left-0 top-1/2 transform -translate-y-1/2 rounded-full p-2 cursor-pointer pointer-events-auto z-[99] font-bold"
          @click="prevImage"
        >
          &lt;
          <!-- Left arrow icon or text -->
        </button>
        <button
          class="bg-white absolute right-0 top-1/2 transform -translate-y-1/2 rounded-full p-2 cursor-pointer pointer-events-auto z-[99] font-bold"
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
        class="aspect-square w-full h-full cursor-pointer"
      >
        <ImageTransition>
          <img
            v-if="images[activeImage].type === 'image'"
            :key="activeImage"
            :src="images[activeImage].image"
            class="object-center object-contain aspect-square"
            id="main-image"
            @mousemove="zoomIn"
            @mouseout="zoomOut"
            @click="showPopup"
            @touchstart="handleTouchStart"
            @touchend="handleTouchEnd"
          />
          <div
            class="relative pointer-events-auto cursor-pointer"
            v-else
            @click="showPopup"
            @touchstart="handleTouchStart"
            @touchend="handleTouchEnd"
          >
            <video
              :src="images[activeImage].video"
              class="object-center object-contain aspect-square"
              id="main-video"
            ></video>
            <div
              class="absolute inset-0 flex items-center justify-center pointer-events-none"
            >
              <button class="text-6xl text-white">▶️</button>
            </div>
          </div>
        </ImageTransition>
        <div
          id="overlay"
          class="absolute top-0 right-0 left-full bg-white z-50 hidden sm:hidden"
        ></div>
      </div>
    </div>
  </div>
  <PopUpTransition>
    <PopUpViewer
      v-if="showPopupFlag"
      :closePopup="closePopup"
      :images="images"
      :activeImage="activeImage"
    ></PopUpViewer>
  </PopUpTransition>
</template>

<style scoped>
#overlay {
  border: 1px solid black;
  width: 550px;
  height: 600px;
  background-repeat: no-repeat;
}
.active-thumb {
  border: 3px solid blue;
}
</style>
