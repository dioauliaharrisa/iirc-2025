<script setup lang="ts">
const images = [
  { name: "clue logo high res", src: "/clue logo high res.png" },
  // { name: "Logo RC", src: "/Logo RC.png" },
  { name: "Logo Kyou BHitam", src: "/Logo_Kyou_BHitam.png" },
  // { name: "Riichi id", src: "/Riichi id.jpeg" },
  { name: "Toko mahjong", src: "/Toko mahjong.jpg" },
  { name: "雀友LOGO", src: "/雀友LOGO.jpg" },
];
</script>

<template>
  <div>
    <SectionHeader />
    <NuxtImg class="p-8" :src="'/Iirc no background.png'" />
    <div class="text-center text-3xl font-semibold leading-tight">
      <p class="text-blue-900">
        Indonesia <span class="text-red-800">International</span>
      </p>
      <p class="text-blue-900">Riichi Championship</p>
      <p class="text-red-800 font-bold">2025</p>
    </div>
    <div class="flex justify-center">
      <NuxtImg src="/Logo RC.png" class="w-48 h-48 object-contain" />
      <NuxtImg
        src="/Riichi ID white background.png"
        class="w-42 h-42 object-contain"
      />
    </div>
    <UMarquee
      pause-on-hover
      reverse
      :overlay="false"
      :ui="{ root: '[--gap:--spacing(4)]', content: 'w-auto py-1' }"
    >
      <div
        v-for="(img, i) in images"
        :key="i"
        class="flex flex-col items-center"
      >
        <NuxtImg
          :src="img.src"
          :alt="img.name"
          class="w-32 h-32 object-contain"
        />
      </div>
    </UMarquee>
  </div>
</template>
