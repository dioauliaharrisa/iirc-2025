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
  console.log("🦆 ~ items:", data);
  items.value = data.map((row, index) => ({
    label: `Round ${index + 1}`,
    content: Object.entries(row).map(([key, value]) => ({
      key,
      value,
    })),
  }));
  console.log("🦆 ~ items:", items.value);
});
</script>

<template>
  <UAccordion
    type="multiple"
    :items="items"
    :ui="{
      item: 'text-white bg-primary border-secondary last:border-b-0',
    }"
  >
    <template #content="{ item }" class="bg-red-500">
      <div
        v-for="(entry, i) in item.content"
        :key="i"
        class="flex justify-between border-5 border-secondary p-8"
      >
        <UAvatarGroup
          size="xl"
          :ui="{
            root: 'transform -rotate-[30deg]',
            base: 'transform rotate-[30deg]',
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
      </div>
    </template>
  </UAccordion>
</template>
