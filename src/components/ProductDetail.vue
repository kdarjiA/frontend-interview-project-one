<script setup>
import { ref } from "vue";

const qty = ref(1);

const updateQty = (type) => {
  if (type === "increment") {
    qty.value++;
  } else if (type === "decrement") {
    if (qty.value === 1) return;
    qty.value--;
  }
};
</script>

<template>
  <div class="mt-10 px-4 sm:mt-16 sm:px-0 lg:mt-0">
    <h1 class="text-3xl font-bold tracking-tight text-gray-900">
      Samsung Galaxy S23 Plus 5G
    </h1>

    <div class="mt-3">
      <h2 class="sr-only">Product information</h2>
      <p class="text-3xl tracking-tight text-gray-900">$140</p>
    </div>

    <div class="mt-6">
      <h3 class="sr-only">Description</h3>

      <div class="space-y-6 text-base text-gray-700">
        <div class="prose prose-sm pb-6" id="disclosure-1">
          <ul role="list">
            <li><b>Sim:</b> DUAL SIM</li>
            <li><b>Condition:</b> LIKE NEW</li>
            <li><b>Color:</b> LAVENDER</li>
            <li><b>Network:</b> UNLOCKED</li>
            <li><b>Network:</b> 512GB</li>
          </ul>
        </div>
      </div>
    </div>

    <form class="mt-6">
      <!-- Colors -->
      <!-- <h3 class="text-sm text-gray-600">Qty</h3> -->
      <div class="custom-number-input h-10 w-32">
        <label
          for="custom-input-number"
          class="w-full text-gray-700 text-sm font-semibold"
          >Qty
        </label>
        <div
          class="flex flex-row h-10 w-full rounded-lg relative bg-transparent mt-1"
        >
          <button
            data-action="decrement"
            class="bg-gray-300 text-gray-600 hover:text-gray-700 hover:bg-gray-400 h-full w-20 rounded-l cursor-pointer outline-none"
            @click="updateQty('decrement')"
            type="button"
          >
            <span class="m-auto text-2xl font-thin">−</span>
          </button>
          <input
            type="number"
            class="outline-none focus:outline-none text-center w-full bg-gray-300 font-semibold text-md hover:text-black focus:text-black md:text-basecursor-default flex items-center text-gray-700 outline-none"
            name="custom-input-number"
            v-model="qty"
          />
          <button
            data-action="increment"
            class="bg-gray-300 text-gray-600 hover:text-gray-700 hover:bg-gray-400 h-full w-20 rounded-r cursor-pointer"
            @click="updateQty('increment')"
            type="button"
          >
            <span class="m-auto text-2xl font-thin">+</span>
          </button>
        </div>
      </div>

      <div class="mt-10 flex">
        <button
          type="button"
          class="flex max-w-full flex-1 items-center justify-center rounded-md border border-transparent bg-yellow-600 px-8 py-3 text-base font-medium text-white hover:bg-yellow-700 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2 focus:ring-offset-gray-50 sm:w-full"
        >
          Add to basket
        </button>
      </div>
    </form>
  </div>
</template>
