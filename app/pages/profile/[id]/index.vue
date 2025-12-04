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
        rank: Number(score),
      };
    });

  const totals = Object.fromEntries(
    Object.entries(foundData).filter(([k]) => k.startsWith("tot_"))
  );

  const chartData2 = Object.keys(totals).map((key, i) => {
    const split = totals[key].split(", ");
    return { date: String(i + 1), score: Number(split[1]) };
  });

  const merged = {
    ...foundData,
    chartData,
    chartData2,
  };

  profile.value = merged;
});

parser2.parse().then((data) => {
  const foundData = data.find((row) => row.id === id) || null;
  const filtered = Object.fromEntries(
    Object.entries(foundData).filter(([key]) => key !== "id")
  );
  profile2.value = filtered;
});

const excludeKeys = [
  "id",
  "firstTotal",
  "secondTotal",
  "thirdTotal",
  "lastTotal",
  "lowestPoint",
];

const filteredProfile = computed(() =>
  Object.fromEntries(
    Object.entries(profile2.value || {}).filter(
      ([key]) => !excludeKeys.includes(key)
    )
  )
);

const categories: Record<string, BulletLegendItemInterface> = {
  score: { name: "Score", color: "#801b1f" },
  // rank: { name: "Rank", color: "#3b82f6" },
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
  rank: {
    type: "circle",
    size: 18,
    color: "#22c55e",
    strokeColor: "#22c55e",
    strokeWidth: 20,
  },
};

const formatKey = (key: string) =>
  key
    .replace(/([A-Z])/g, " $1") // insert spaces
    .replace(/^./, (c) => c.toUpperCase()); // capitalize first letter
</script>

<template>
  <div class="flex flex-col gap-4">
    <div class="flex gap-4 p-4 bg-[#99484c] text-white items-center">
      <UAvatar
        :src="profile?.urlPicture"
        alt="Benjamin Canac"
        class="w-18 h-18"
      />
      <div class="flex flex-col gap-2">
        <div class="text-xl font-semibold">{{ profile?.name }}</div>
        <NuxtImg
          width="30"
          :alt="profile?.name"
          :src="`https://purecatamphetamine.github.io/country-flag-icons/3x2/${profile?.country}.svg`"
        />
      </div>
    </div>
    <div class="flex overflow-x-auto snap-x snap-mandatory gap-4 pb-4">
      <!-- Chart 1 -->
      <div class="snap-center shrink-0 w-full">
        <div class="">
          <LineChart
            :data="profile?.chartData"
            :height="250"
            x-label="Hanchan"
            :x-num-ticks="24"
            :categories="categories"
            :x-formatter="xFormatter"
            y-label="Rank"
            :y-domain="[4, 1]"
            :y-tick-line="true"
            :y-num-ticks="4"
            :y-domain-line="true"
            :y-formatter="yFormatter"
            :y-grid-line="true"
            :curve-type="CurveType.Linear"
            :marker-config="MarkerConfig"
            :hide-y-axis="false"
            hide-legend
          >
            <template #tooltip="{ values }">
              <div>Score: {{ String(values?.rank) }}</div>
            </template>
          </LineChart>
        </div>
      </div>
      <!-- Chart 2 -->
      <div class="snap-center shrink-0 w-full">
        <div class="">
          <!-- :y-num-ticks="4" -->
          <LineChart
            :data="profile?.chartData2"
            :height="250"
            x-label="Hanchan"
            y-label="Total Point"
            :x-num-ticks="12"
            :y-num-ticks="7"
            :categories="categories"
            :x-formatter="xFormatter"
            :y-formatter="yFormatter"
            :y-domain="[-150, 150]"
            :y-grid-line="true"
            :curve-type="CurveType.Linear"
            :hide-y-axis="false"
            hide-legend
          >
          </LineChart>
        </div>
      </div>
    </div>
    <div class="flex flex-col items-center bg-[#99484c] p-4 text-white">
      <h3 class="text-xl font-semibold">Total Ranking</h3>
      <p class="text-lg font-semibold">
        {{
          `${profile2?.firstTotal}/${profile2?.secondTotal}/${profile2?.thirdTotal}/${profile2?.lastTotal}`
        }}
      </p>
    </div>
    <div class="grid grid-cols-3 gap-2 p-4">
      <UCard
        v-for="(value, key) in filteredProfile"
        :key="key"
        :ui="{
          root: 'shadow-sm bg-primary',
          body: 'py-4 sm:p-4 ', // remove default padding
        }"
      >
        <div
          class="flex flex-col justify-between items-center h-20 text-center"
        >
          <span
            class="text-[12px] leading-tight capitalize inline-block text-gray-200"
          >
            {{ formatKey(key) }}
          </span>
          <p class="text-lg font-semibold leading-tight pb-2 text-white">
            {{ value }}
          </p>
        </div>
      </UCard>
    </div>
  </div>
  <UBanner class="h-[50px]" title="End of the table" />
</template>

<style scoped>
.markers:deep(*[stroke="#801b1f"]) {
  marker: url("#circle-marker-score");
}
</style>
