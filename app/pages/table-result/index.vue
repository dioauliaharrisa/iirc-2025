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
    content: Object.entries(row).map(([key, value]) => ({
      key,
      value,
    })),
  }));
});
const active = ref(["0"]);
</script>

<template>
  <div class="py-4">
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
          class="flex justify-start border-1 border-secondary py-8"
        >
          <UAvatarGroup
            size="xl"
            :ui="{
              root: ' transform rotate-150',
              base: 'transform -rotate-150 ring--1 ring-offset-0',
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
          <div class="flex -space-x-4 transform rotate-90 ml-6">
            <div
              v-for="(score, j) in ['Andy', 'Fai', 'Yi', 'Yo']"
              :key="j"
              class="w-10 h-10 flex items-center justify-start text-secondary text-xs transform -rotate-90"
            >
              {{ score }}
            </div>
          </div>
          <div class="flex -space-x-4 transform rotate-90 ml-6">
            <div
              v-for="(score, j) in ['+30.5', '5', '-12', '-40']"
              :key="j"
              class="w-10 h-10 flex items-center justify-center text-secondary text-xs font-bold transform -rotate-90"
            >
              {{ score }}
            </div>
          </div>
        </div>
      </template>
    </UAccordion>
  </div>
</template>
