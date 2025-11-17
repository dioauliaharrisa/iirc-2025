<script setup lang="ts">
import PublicGoogleSheetsParser from "public-google-sheets-parser";
const options = { sheetName: "Display_Leaderboard_P", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const items = ref<{ [key: string]: string }[]>([]);

parser.parse().then((data) => {
  console.log("🦆 ~ data:", data);
  items.value = data.map((item) => ({
    ...item,
    urlPhoto: item.urlPhoto
      ? item.urlPhoto.replace("imgur.com/", "i.imgur.com/") + ".jpg"
      : null,
  }));
  console.log("🦆 ~ items:", items);
});
</script>

<template>
  <UTable :data="items || []" class="w-dvw">
    <template #name-cell="{ row }">
      <UAvatar
        :src="row.original.urlPhoto"
        size="lg"
        :alt="`${row.original.name} avatar`"
      />
    </template>
  </UTable>
</template>
