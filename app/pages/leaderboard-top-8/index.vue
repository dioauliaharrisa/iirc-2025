<script setup lang="ts">
import PublicGoogleSheetsParser from "public-google-sheets-parser";
const options = { sheetName: "Display_Leaderboard_Top8", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const items = ref<{ [key: string]: string }[]>([]);

parser.parse().then((data) => {
  items.value = data.map((item) => {
    return {
      ...item,
      urlPhoto: item.urlPhoto,
    };
  });
});
const UAvatar = resolveComponent("UAvatar");

const router = useRouter();

const columns = [
  {
    accessorKey: "urlPhoto",
    header: "Photo",
    cell: ({ row }) => {
      return h(
        "div",
        {
          class: "flex items-center gap-3",
          onClick: () => router.push(`/profile/${row.original.playerID}`),
        },
        [
          h(UAvatar, {
            src: row.original.urlPhoto,
            size: "3xl",
          }),
          h("div", undefined, [
            h(
              "p",
              { class: "font-medium text-highlighted" },
              row.original.name
            ),
            h(NuxtImg, {
              src:
                row.original?.country === "TW"
                  ? "https://upload.wikimedia.org/wikipedia/commons/b/b7/Flag_of_Chinese_Taipei_for_Olympic_Games.svg"
                  : `https://purecatamphetamine.github.io/country-flag-icons/3x2/${row.original?.country}.svg`,
              class: "w-6 h-6 object-cover rounded-md",
              alt: row.original.name,
              format: "webp",
            }),
          ]),
        ]
      );
    },
  },

  {
    accessorKey: "scoreTotalP",
    header: "Score",
  },
];
</script>

<template>
  <UTable :data="items || []" :columns="columns"> </UTable>
</template>
