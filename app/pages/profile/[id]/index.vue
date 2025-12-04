<script setup lang="ts">
// import type { AccordionItem } from "@nuxt/ui";

// import photos from "../../../codeUrlPhoto.json";

import PublicGoogleSheetsParser from "public-google-sheets-parser";

const options1 = {
  sheetName: "Display_Profile_P1",
  useFormat: true,
};
const options2 = {
  sheetName: "Display_Profile_P2", // 4545
  useFormat: true,
};
const parser1 = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options1
);
const parser2 = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options2
);

const route = useRoute();
const id = route.params.id as string;

const profile = ref<{ [key: string]: string } | null>(null);
const profile2 = ref<{ [key: string]: string } | null>(null);

parser1.parse().then((data) => {
  const foundData = data.find((row) => row.id === id) || null;

  const chartData = Object.entries(foundData)
    .filter(([key]) => !isNaN(Number(key)))
    .map(([key, value], index) => {
      const [rank, score] = value.split(",").map((v) => v.trim());
      return {
        date: `${index + 1}`,
        score: Number(rank),
      };
    });

  const totals = Object.fromEntries(
    Object.entries(foundData).filter(([k]) => k.startsWith("tot_"))
  );

  const chartData2 = Object.keys(totals).map((key) => {
    const [a, b] = totals[key].split(",").map(Number);
    return { label: key, a, b };
  });

  const merged = {
    ...foundData,
    chartData,
    chartData2,
  };

  profile.value = merged;
  console.log("🦆 ~ profile.value:", profile.value);
});

parser2.parse().then((data) => {
  const foundData = data.find((row) => row.id === id) || null;
  const filtered = Object.fromEntries(
    Object.entries(foundData).filter(([key]) => key !== "id")
  );
  profile2.value = filtered;
  console.log("🦆 ~ profile2.value:", profile2.value);
});

const excludeKeys = [
  "id",
  "firstTotal",
  "secondTotal",
  "thirdTotal",
  "lastTotal",
  "pointLowest",
];

const filteredProfile = computed(() =>
  Object.fromEntries(
    Object.entries(profile2.value || {}).filter(
      ([key]) => !excludeKeys.includes(key)
    )
  )
);

const categories: Record<string, BulletLegendItemInterface> = {
  score: { name: "Score", color: "#22c55e" },
};
const xFormatter = (tick: number, _i?: number, _ticks?: number[]): string => {
  return String(profile?.value?.chartData?.[tick]?.date ?? "");
};
const yFormatter = (value: number) => String(value);

interface MarkerConfig {
  type?: "circle" | "square" | "triangle" | "diamond";
  size?: number;
  strokeWidth?: number;
  color?: string;
  strokeColor?: string;
}
const MarkerConfig = {
  type: "circle",
  size: 18,
  color: "#22c55e",
  strokeColor: "#22c55e",
  strokeWidth: 20,
};

const formatKey = (key: string) =>
  key
    .replace(/([A-Z])/g, " $1") // insert spaces
    .replace(/^./, (c) => c.toUpperCase()); // capitalize first letter
</script>

<template>
  <div class="bg-[#FFFEFA] p-4">
    <div class="flex gap-4">
      <div>
        <UAvatar
          :src="profile?.urlPicture"
          alt="Benjamin Canac"
          class="w-18 h-18"
        />
      </div>
      <div>
        <div class="text-xl font-semibold">{{ profile?.name }}</div>
        <!-- <div>{{ profile?.country }}</div> -->
        <NuxtImg
          width="30"
          :alt="profile?.name"
          :src="`https://purecatamphetamine.github.io/country-flag-icons/3x2/${profile?.country}.svg`"
        />
      </div>
    </div>
    <div class="p-4 py-8">
      <LineChart
        :data="profile?.chartData"
        :height="150"
        y-label="Rank"
        :x-num-ticks="24"
        :y-domain="[4, 1]"
        :y-num-ticks="4"
        :categories="categories"
        :x-formatter="xFormatter"
        :y-formatter="yFormatter"
        :y-grid-line="true"
        :curve-type="CurveType.Linear"
        :marker-config="MarkerConfig"
        :hide-y-axis="false"
      />
    </div>
    <div class="flex flex-col items-center">
      <h3 class="text-xl font-semibold">Total Ranking</h3>
      <p class="text-lg font-semibold">
        {{
          `${profile2?.firstTotal}/${profile2?.secondTotal}/${profile2?.thirdTotal}/${profile2?.lastTotal}`
        }}
      </p>
    </div>
    <div class="grid grid-cols-3 gap-2 p-2">
      <UCard
        v-for="(value, key) in filteredProfile"
        :key="key"
        :ui="{
          base: 'rounded-md',
          body: 'py-4 sm:p-4', // remove default padding
        }"
      >
        <div class="flex flex-col justify-evenly items-center h-16 text-center">
          <p class="text-[12px] text-gray-500 leading-tight capitalize">
            {{ formatKey(key) }}
          </p>
          <p class="text-lg font-semibold leading-tight">
            {{ value }}
          </p>
        </div>
      </UCard>
    </div>
  </div>
</template>

<style scoped>
.markers:deep(*[stroke="#22c55e"]) {
  marker: url("#circle-marker-score");
}
</style>
