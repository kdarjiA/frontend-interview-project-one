<script setup>
import { computed, onMounted, ref, watch } from "vue";
import ImageTransition from "./ImageTransition.vue";

const { images, activeImage, closePopup } = defineProps([
  "images",
  "activeImage",
  "closePopup",
]);
const modalActiveImage = ref(activeImage);
const activeTab = ref(images[modalActiveImage.value].type);
const touchStartX = ref(0);
const touchEndX = ref(0);
const zoomImage = ref(false);
const clickCount = ref(0);
const delay = 300;
const timer = ref(null);

const changeImage = (image) => {
  modalActiveImage.value = image;
  zoomImage.value = false;
};
const data = computed(() => {
  return getFilteredData(images);
});

const getFilteredData = (images) => {
  let filteredData = {};
  Object.keys(images).forEach((image) => {
    if (images[image].type === activeTab.value) {
      filteredData[image] = images[image];
    }
  });
  return filteredData;
};

const handleTouchStart = (e) => {
  if (!zoomImage.value) {
    touchStartX.value = e.touches[0].clientX;
  }
};

const handleTouchEnd = (e) => {
  if (!zoomImage.value) {
    touchEndX.value = e.changedTouches[0].clientX;
    handleSwipe();
  }
};

const handleSwipe = () => {
  const threshold = 50; // Adjust this threshold as needed
  const deltaX = touchEndX.value - touchStartX.value;

  if (deltaX > threshold) {
    // Swipe left, change to the previous image
    if (modalActiveImage.value === 0) return;
    modalActiveImage.value--;
  } else if (deltaX < -threshold) {
    // Swipe right, change to the next image
    if (modalActiveImage.value === Object.keys(images).length - 1) return;
    modalActiveImage.value++;
  }
};

const handleMouseMove = (e) => {
  if (zoomImage.value) {
    magnifyImage(e);
  }
};

const magnifyImage = (e) => {
  const magnifyImg = document.getElementById("magnifying-img");
  const magnifyArea = document.getElementById("magnifying-area");

  let clientX = e.clientX - magnifyArea.offsetLeft;
  let clientY = e.clientY - magnifyArea.offsetTop;

  let mWidth = magnifyArea.offsetWidth;
  let mHeight = magnifyArea.offsetHeight;

  clientX = (clientX / mWidth) * 100;
  clientY = (clientY / mHeight) * 100;

  console.log(clientX, clientY);

  magnifyImg.style.transform =
    "translate(-" + (clientX) + "%, -" + (clientY) + "%) scale(1.5)";
};

const handleMouseLeave = () => {
  const magnifyImg = document.getElementById("magnifying-img");
  magnifyImg.style.transform = "translate(-50%, -50%) scale(1)";
};

const handleZoom = (e) => {
  const magnifyImg = document.getElementById("magnifying-img");
  zoomImage.value = !zoomImage.value;
  if (!zoomImage.value) {
    handleMouseLeave();
    magnifyImg.src = `${images[modalActiveImage.value].image}`;
  } else {
    magnifyImg.src = `${images[modalActiveImage.value].large}`;
    magnifyImage(e);
  }
};

const handleDblClick = (e) => {
  if (screen.width <= 760) {
    handleZoom(e);
  }
};

const handleClick = (e) => {
  if (screen.width > 760) {
    handleZoom(e);
  }
};

const handleTouchMove = (e) => {
  if (zoomImage.value) {
    magnifyImage(e.touches[0]);
  }
};

watch(activeTab, () => {
  let filteredData = getFilteredData(images);
  modalActiveImage.value = Object.keys(filteredData)[0];
});
</script>
<template>
  <div
    class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 w-full h-full z-[99] p-4"
  >
    <div class="bg-white rounded-lg p-8 lg:w-3/4 relative sm:h-full w-full overflow-y-auto">
      <button
        class="absolute lg:top-4 sm:top-7 right-4 text-gray-500 hover:text-gray-700"
        type="button"
        @click="closePopup"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6 text-gray-600 hover:text-gray-600"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>

      <!-- Tabs to switch between photos and videos -->
      <div class="flex space-x-4">
        <button
          @click="activeTab = 'image'"
          :class="{
            'text-yellow-600 border-b-2 border-yellow-600':
              activeTab === 'image',
            'text-gray-400 hover:text-gray-600': activeTab !== 'image',
          }"
        >
          Photos
        </button>
        <button
          @click="activeTab = 'video'"
          :class="{
            'text-yellow-600 border-b-2 border-yellow-600':
              activeTab === 'video',
            'text-gray-400 hover:text-gray-600': activeTab !== 'video',
          }"
        >
          Videos
        </button>
      </div>
      <hr class="mb-4" />

      <main class="container mx-auto p-4">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
          <div class="lg:col-span-2">
            <div class="overflow-hidden">
              <figure
                @mousemove="handleMouseMove"
                v-if="data[modalActiveImage].type === 'image'"
                id="magnifying-area"
                class="overflow-hidden relative pointer-events-auto"
                :class="!zoomImage ? 'cursor-zoom-in' : 'cursor-zoom-out'"
                @click="handleClick"
                @touchstart="handleTouchStart"
                @touchend="handleTouchEnd"
                @touchmove="handleTouchMove"
                @dblclick="handleDblClick"
              >
                <ImageTransition>
                  <img
                    :key="modalActiveImage"
                    :src="data[modalActiveImage].image"
                    alt="Product Image"
                    class="object-center object-contain aspect-square h-full w-full pointer-events-none absolute"
                    id="magnifying-img"
                  />
                </ImageTransition>
              </figure>
              <div v-else>
                <ImageTransition>
                  <video
                    :key="modalActiveImage"
                    :src="data[modalActiveImage].video"
                    class="object-center object-contain aspect-video"
                    id="popup-video"
                    controls
                  ></video>
                </ImageTransition>
              </div>
            </div>
          </div>

          <div class="lg:col-span-1">
            <div class="mb-8 hidden md:block">
              <h1 class="text-2xl font-semibold">Samsung Galaxy S23 Plus 5G</h1>
              <ul role="list">
                <li><b>Sim:</b> DUAL SIM</li>
                <li><b>Condition:</b> LIKE NEW</li>
                <li><b>Color:</b> LAVENDER</li>
                <li><b>Network:</b> UNLOCKED</li>
                <li><b>Network:</b> 512GB</li>
              </ul>
            </div>
            <div class="grid grid-cols-3 gap-2">
              <div v-for="(image, key) in data">
                <div
                  class="border rounded-lg overflow-hidden cursor-pointer"
                  v-if="image"
                  :class="{
                    'ring ring-yellow-500 ring-2': key == modalActiveImage,
                  }"
                  @click="changeImage(key)"
                >
                  <img
                    v-if="image.type === 'image'"
                    :src="image.thumb"
                    alt="Thumbnail 1"
                    class="object-center object-contain aspect-square w-full h-full"
                  />
                  <div class="relative pointer-events-auto" v-else>
                    <video
                      :src="image.video"
                      class="object-center object-contain aspect-square"
                    ></video>
                    <div
                      class="absolute inset-0 flex items-center justify-center pointer-events-none"
                    >
                      <button class="text-2xl text-white">▶️</button>
                    </div>
                  </div>
                </div>
              </div>
              <!-- Add more thumbnail images as needed -->
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>
<style scoped>
#magnifying-area {
  overflow: hidden;
  position: relative;
  height: 500px;
  /* width: 600px; */
}

#magnifying-img {
  max-width: 100%;
  min-width: 100%;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
}
</style>
