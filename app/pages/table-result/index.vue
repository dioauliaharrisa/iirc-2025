<script setup lang="ts">
// import type { AccordionItem } from "@nuxt/ui";
import PublicGoogleSheetsParser from "public-google-sheets-parser";

const options = { sheetName: "Display_Preliminary_Match_Up", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);
const items = ref<{ [key: string]: string }[]>([]);
parser.parse().then((data) => {
  items.value = data.map((row, index) => ({
    label: `Round ${index + 1}`,
    content: Object.entries(row).map(([key, value]) => {
      const parts = value.split(",").map((v) => v.trim());

      const grouped = [];
      for (let i = 0; i < parts.length; i += 3) {
        grouped.push({
          id: parts[i],
          name: parts[i + 1],
          score: parts[i + 2],
        });
      }
      console.log("🦆 ~ grouped:", grouped);
      return { key, grouped };
    }),
  }));
});
const active = ref(["0"]);
</script>

<template>
  <div>
    <UHeader
      :ui="{
        root: 'bg-gray-100',
        toggle: 'hidden', // hides hamburger button
      }"
    >
      <template #left>
        <NuxtImg
          class="h-12 w-auto"
          :src="'Iirc background.png'"
          :toggle="false"
        />
      </template>
      <template #right>
        <p class="text-2xl text-primary font-semibold">Table Result</p>
      </template>
    </UHeader>
    <div>
      <UAccordion
        v-model="active"
        type="multiple"
        :items="items"
        :ui="{
          item: 'text-gray-800 bg-gray-100 border-secondary last:border-b-0',
        }"
      >
        <template #content="{ item }">
          <div
            v-for="(entry, i) in item.content"
            :key="i"
            class="flex justify-start border border-secondary py-8"
          >
            <UAvatarGroup
              size="xl"
              :ui="{
                root: ' transform rotate-150 w-[25vw]',
                base: 'transform -rotate-150 ring-0 ring-offset-0',
              }"
            >
              <UAvatar
                src="https://github.com/benjamincanac.png"
                alt="Benjamin Canac"
              />
              <UAvatar src="https://github.com/romhml.png" alt="Romain Hamel" />
              <UAvatar src="https://github.com/noook.png" alt="Neil Richter" />
              <UAvatar src="https://github.com/noook.png" alt="Neil Richter" />
            </UAvatarGroup>
            <div class="flex transform rotate-90 ml-6 w-[25vw]">
              <div
                v-for="(player, j) in entry.grouped"
                :key="player.id + '-name'"
                class="w-20 flex items-center justify-start text-secondary text-xs transform -rotate-90"
              >
                {{ player.name }}
              </div>
            </div>

            <!-- player scores -->
            <div class="flex -space-x-4 transform rotate-90 ml-6">
              <div
                v-for="(player, j) in entry.grouped"
                :key="player.id + '-score'"
                class="w-10 flex items-center justify-center text-secondary text-xs font-bold transform -rotate-90"
              >
                {{ player.score }}
              </div>
            </div>
          </div>
        </template>
      </UAccordion>
    </div>
  </div>
</template>
