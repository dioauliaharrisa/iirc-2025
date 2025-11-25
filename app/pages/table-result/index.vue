<script setup lang="ts">
// import type { AccordionItem } from "@nuxt/ui";
import photos from "../../../codeUrlPhoto.json";
import PublicGoogleSheetsParser from "public-google-sheets-parser";

const options = { sheetName: "Display_Match_Up", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);
const items = ref<{ [key: string]: string }[]>([]);
parser.parse().then((data) => {
  items.value = data.map((row, index) => ({
    label: `Hanchan ${index + 1}`,
    content: Object.entries(row).map(([key, value]) => {
      console.log("🦆 ~ value:", value);
      const parts = value.split(",").map((v) => v.trim());

      const grouped = [];
      for (let i = 0; i < parts.length; i += 3) {
        grouped.push({
          id: parts[i],
          name: parts[i + 1],
          score: parts[i + 2],
          urlPhoto: photos[parts[i]] || null,
        });
      }
      grouped.sort((a, b) => b.score - a.score);

      return { key, grouped };
    }),
  }));
  console.log("🦆 ~ items:", items.value);
});
const active = ref(["0"]);
</script>

<template>
  <div>
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
            class="flex flex-col justify-center border border-secondary"
          >
            <div
              v-for="(each, ii) in entry.grouped"
              :key="ii"
              class="p-2 flex items-center gap-4 justify-between"
            >
              <UAvatar
                :src="each.urlPhoto"
                alt="Benjamin Canac"
                class="w-18 h-18"
              />
              <p @click="$router.push(`profile/${each.id}`)" class="flex-start">
                {{ each.name }}
              </p>
              <p class="font-semibold">{{ each.score }}</p>
            </div>
          </div>
        </template>
      </UAccordion>
    </div>
  </div>
</template>
