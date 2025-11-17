<script setup lang="ts">
import PublicGoogleSheetsParser from "public-google-sheets-parser";
const options = { sheetName: "Display_Leaderboard_P", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const items = ref<{ [key: string]: string }[]>([]);

parser.parse().then((data) => {
  items.value = data
    .map((item) => {
      return {
        ...item,
        urlPhoto: item.urlPhoto
          ? item.urlPhoto.replace("imgur.com/", "i.imgur.com/") + ".jpg"
          : null,
      };
    })
    .sort((a, b) => Number(b.scoreTotalP) - Number(a.scoreTotalP));
});
const UAvatar = resolveComponent("UAvatar");
const columns = [
  {
    accessorKey: "urlPhoto",
    header: "Photo",
    cell: ({ row }) => {
      console.log("🦆 ~ row:", row);
      return h("div", { class: "flex items-center gap-3" }, [
        h(UAvatar, {
          src: row.original.urlPhoto,
          size: "3xl",
        }),
        h("div", undefined, [
          h("p", { class: "font-medium text-highlighted" }, row.original.name),
          h("p", { class: "" }, `@${row.original.username}`),
        ]),
      ]);
    },
  },
  {
    accessorKey: "scoreTotalP",
    header: "Score",
  },
];
</script>

<template>
  <UTable :data="items || []" :columns="columns" class="">
    <!-- <template #url-photo-cell="{ row }">
      <UAvatar
        :src="row.original.urlPhoto"
        size="3xl"
        :alt="`${row.original.name} avatar`"
      />
    </template> -->
  </UTable>
</template>
