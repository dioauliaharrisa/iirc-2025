<script setup lang="ts">
const images = [
  { name: "clue logo high res", src: "/clue logo high res.png" },
  // { name: "Logo RC", src: "/Logo RC.png" },
  { name: "Logo Kyou BHitam", src: "/Logo_Kyou_BHitam.png" },
  // { name: "Riichi id", src: "/Riichi id.jpeg" },
  { name: "Toko mahjong", src: "/Toko mahjong.jpg" },
  { name: "雀友LOGO", src: "/雀友LOGO.jpg" },
  { name: "Ynsnobg", src: "Ynsnobg.png" },
];
const schedule = [
  {
    day: "Saturday, 6 December 2025",
    items: [
      ["08:30 - 08:50", "Registration"],
      ["08:50 - 09:00", "Opening Speech"],
      ["09:00 - 10:30", "1st Hanchan"],
      ["10:30 - 12:00", "2nd Hanchan"],
      ["12:00 - 13:30", "3rd Hanchan"],
      ["13:30 - 14:30", "Lunch Break"],
      ["14:30 - 16:00", "4th Hanchan"],
      ["16:00 - 17:30", "5th Hanchan"],
      ["17:30 - 19:00", "6th Hanchan"],
    ],
  },
  {
    day: "Sunday, 7 December 2025",
    items: [
      ["09:00 - 10:30", "7th Hanchan"],
      ["10:30 - 12:00", "8th Hanchan"],
      ["12:00 - 13:00", "Lunch Break"],
      ["13:30 - 14:30", "9th Hanchan"],
      ["14:30 - 16:00", "10th Hanchan"],
      ["16:00 - 17:30", "11th Hanchan"],
      ["17:30 - 19:00", "12th Hanchan"],
      ["19:00 - 19:30", "Closing Ceremony"],
    ],
  },
];
</script>

<template>
  <div class="">
    <NuxtImg class="p-8 w-[50vh]" :src="'/Iirc no background.png'" />
    <div class="text-center text-3xl font-semibold leading-tight">
      <p class="text-blue-900">
        Indonesia <span class="text-red-800">International</span>
      </p>
      <p class="text-blue-900">Riichi Championship</p>
      <p class="text-red-800 font-bold">2025</p>
    </div>
    <div
      class="grid grid-rows-2 grid-cols-2 place-items-center bg-secondary h-[20vh] text-white font-bold text-xl"
    >
      <UModal>
        <div>Schedule</div>
        <template #content>
          <UCard>
            <div class="max-w-2xl mx-auto text-sm text-white">
              <div v-for="(day, d) in schedule" :key="d" class="mb-6">
                <h2 class="text-lg font-bold text-center bg-primary py-2">
                  {{ day.day }}
                </h2>
                <div
                  class="grid grid-cols-2 gap-x-6 gap-y-2 bg-gray-100 p-4 text-gray-900"
                >
                  <template v-for="(item, i) in day.items" :key="i">
                    <div>{{ item[0] }}</div>
                    <div>
                      <span
                        v-html="
                          item[1].includes('Hanchan')
                            ? item[1]
                            : `<b>${item[1]}</b>`
                        "
                      ></span>
                    </div>
                  </template>
                </div>
              </div>
            </div>
          </UCard>
        </template>
      </UModal>

      <div class="cursor-pointer" @click="$router.push('leaderboard')">
        Leaderboard
      </div>
      <div class="cursor-pointer" @click="$router.push('table-result')">
        Table Results
      </div>
      <div class="cursor-pointer" @click="$router.push('table-result')">
        Top 8 Leaderboard
      </div>
    </div>
    <div class="flex justify-center">
      <NuxtImg
        src="/Riichi ID white background.png"
        class="w-24 h-24 object-contain"
      />
      <NuxtImg src="/Logo RC.png" class="w-24 h-24 object-contain" />
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
          class="w-16 h-16 object-contain"
        />
      </div>
    </UMarquee>
  </div>
</template>
