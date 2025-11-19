<script setup lang="ts">
import type { NavigationMenuItem } from "@nuxt/ui";

const showModal = ref(false);
const items = computed<NavigationMenuItem[]>(() => [
  {
    label: "Schedule",
    click: () => (showModal.value = true),
    icon: "i-lucide-book-open",
  },
]);

const schedule = [
  {
    day: "Saturday",
    events: [
      { time: "08.00", activity: "Registration open" },
      { time: "09.00", activity: "Opening Ceremony" },
      { time: "10.00", activity: "Keynote Speech" },
      { time: "11.00", activity: "Workshop Session" },
    ],
  },
  {
    day: "Sunday",
    events: [
      { time: "08.00", activity: "Breakfast & Networking" },
      { time: "09.30", activity: "Panel Discussion" },
      { time: "11.00", activity: "Closing Remarks" },
    ],
  },
];
</script>

<template>
  <UHeader
    :ui="{
      root: 'bg-primary sticky bottom-0 z-50 ',
      toggle: 'hidden', // hides hamburger button
    }"
  >
    <template #left>
      <UIcon
        name="i-material-symbols:home"
        class="size-5 text-white"
        @click="$router.push('/')"
      />
    </template>
    <template #body>
      <p class="text-lg text-white font-semibold text-right">
        Indonesia International Riichi Competition
      </p>
    </template>
    <template #right>
      <NuxtImg
        class="h-12 w-auto"
        :src="'Iirc background.png'"
        :toggle="false"
      />
    </template>
  </UHeader>
</template>
