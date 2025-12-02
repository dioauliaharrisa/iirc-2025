<script setup lang="ts">
import PublicGoogleSheetsParser from "public-google-sheets-parser";
const options = { sheetName: "Display_Leaderboard_Top8", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const items = ref<{ [key: string]: string }[]>([]);

parser.parse().then((data) => {
  items.value = data;
  console.log("🦆 ~ items.value:", items.value);
});
const UAvatar = resolveComponent("UAvatar");

const router = useRouter();

const columns = [
  {
    accessorKey: "urlPhoto",
    header: "Name",
    cell: ({ row }) => {
      console.log("🦆 ~ row:", row.original);
      const isSpecialRow = row.original.rank === "0";

      const playerEntry = h(
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
              class: "w-8 h-6 object-cover border border-gray-200",
              alt: row.original.name,
              format: "webp",
            }),
          ]),
        ]
      );
      if (isSpecialRow) {
        return h("div", { class: "flex flex-col gap-2 bg-primary" }, [
          h(
            "div",
            {
              class: "flex items-center gap-3 h-16",
            },
            [
              h("div", undefined, [
                h(
                  "p",
                  { class: "font-medium text-highlighted" },
                  row.original.name
                ),
              ]),
            ]
          ),
        ]);
      } else {
        return playerEntry;
      }
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
