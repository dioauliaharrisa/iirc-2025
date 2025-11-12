<script setup lang="ts">
// import type { AccordionItem } from "@nuxt/ui";
import PublicGoogleSheetsParser from "public-google-sheets-parser";

const options = { sheetName: "Display_Profile", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const route = useRoute();
const id = route.params.id as string;

const profile = ref<{ [key: string]: string } | null>(null);

parser.parse().then((data) => {
  const foundData = data.find((row) => row.id === id) || null;
  console.log("🦆 ~ foundData:", foundData);

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
  console.log("🦆 ~ profile:", profile.value.chartData);
});

const categories: Record<string, BulletLegendItemInterface> = {
  score: { name: "Rank", color: "#3b82f6" },
};
const xFormatter = (tick: number, _i?: number, _ticks?: number[]): string => {
  return String(profile?.value?.chartData?.[tick]?.date ?? "");
};
const yFormatter = (value: number) => String(value);
</script>

<template>
  <div>
    <div>{{ profile?.name }}</div>
    <div>{{ profile?.country }}</div>
    <div>{{ profile?.name }}</div>
    <LineChart
      :data="profile?.chartData"
      :height="200"
      y-label="Rank"
      :x-num-ticks="24"
      :y-num-ticks="4"
      :y-domain="[4, 1]"
      :categories="categories"
      :x-formatter="xFormatter"
      :y-formatter="yFormatter"
      :y-grid-line="true"
      :curve-type="CurveType.Linear"
    />
  </div>
</template>
