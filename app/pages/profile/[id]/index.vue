<script setup lang="ts">
// import type { AccordionItem } from "@nuxt/ui";
import PublicGoogleSheetsParser from "public-google-sheets-parser";

const options1 = {
  sheetName: "Display_Profile_P1",
  useFormat: true,
};
const options2 = {
  sheetName: "Display_Profile_P2",
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
  console.log("🦆 ~ data:", data);
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

  const merged = {
    ...foundData,
    chartData,
  };

  profile.value = merged;
  console.log("🦆 ~ profile:", profile.value);
});

parser2.parse().then((data) => {
  console.log("🦆 ~ data:", data);
  const foundData = data.find((row) => row.id === id) || null;

  profile2.value = foundData;
});

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
</script>

<template>
  <div class="bg-[#FFFEFA]">
    <div>
      <div>{{ profile?.name }}</div>
      <div>{{ profile?.country }}</div>
    </div>
    <div>{{ profile?.name }}</div>
    <LineChart
      :data="profile?.chartData"
      :height="100"
      y-label="Rank"
      :x-num-ticks="24"
      :y-num-ticks="4"
      :y-domain="[4, 1]"
      :categories="categories"
      :x-formatter="xFormatter"
      :y-formatter="yFormatter"
      :y-grid-line="true"
      :curve-type="CurveType.Linear"
      :marker-config="MarkerConfig"
      :hide-y-axis="true"
    />
    <div class="grid grid-cols-3 gap-2 p-2">
      <UCard
        v-for="(value, key) in profile2"
        :key="key"
        class="text-center px-2 py-3"
        :ui="{
          base: 'rounded-md',
          body: { padding: '' }, // remove default padding
        }"
      >
        <p class="text-[10px] text-gray-500 leading-tight capitalize">
          {{ key }}
        </p>
        <p class="text-sm font-semibold leading-tight">
          {{ value }}
        </p>
      </UCard>
    </div>
  </div>
</template>

<style scoped>
.markers:deep(*[stroke="#22c55e"]) {
  marker: url("#circle-marker-score");
}
</style>
