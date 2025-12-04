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
});
const UAvatar = resolveComponent("UAvatar");

const router = useRouter();

const medal = (rank) => {
  const r = Number(rank);

  if (r >= 1 && r <= 3) return "🏆"; // Gold
  if (r >= 4 && r <= 7) return "🥇"; // Gold
  if (r >= 8 && r <= 14) return "🥈"; // Gold

  return "";
};

const columns = [
  {
    accessorKey: "rank",
    header: "Rank",
    cell: ({ row }) => {
      if (row.original.rank === "0") return;
      return h("div", {}, [
        h("span", { class: "text-lg font-medium" }, row.original.rank),
        // h("span", { class: "ml-2 text-lg" }, `${medal(row.original?.rank)}`),
      ]);
    },
  },
  {
    accessorKey: "urlPhoto",
    header: "Name",
    cell: ({ row }) => {
      const isSpecialRow = row.original.rank === "0";

      const playerEntry = h(
        "div",
        {
          class: "flex items-center gap-3 max-w-96",
          onClick: () => router.push(`/profile/${row.original.playerID}`),
        },
        [
          h(UAvatar, {
            src: row.original.urlPhoto,
            size: "3xl",
          }),
          h("div", { class: "flex flex-col" }, [
            h("div", { class: "flex items-center gap-2" }, [
              h(
                "p",
                { class: "font-medium text-highlighted whitespace-normal" },
                row.original.name
              ),
              // h(
              //   "p",
              //   { class: "font-medium text-highlighted" },
              //   `${medal(row.original?.rank)}`
              // ),
            ]),
            h(NuxtImg, {
              src: `https://purecatamphetamine.github.io/country-flag-icons/3x2/${row.original?.country}.svg`,
              class: "w-8 h-6 object-cover border border-gray-200",
              alt: row.original.name,
              format: "webp",
            }),
          ]),
        ]
      );

      if (isSpecialRow) {
        return h("div", { class: "flex flex-col gap-2" }, [
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
  <div>
    <UTable :data="items || []" :columns="columns"> </UTable>
    <UBanner class="h-[50px]" title="End of the table" />
  </div>
</template>
