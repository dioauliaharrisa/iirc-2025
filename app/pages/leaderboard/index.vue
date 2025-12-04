<script setup lang="ts">
import PublicGoogleSheetsParser from "public-google-sheets-parser";
const options = { sheetName: "Display_Leaderboard_P", useFormat: true };
const parser = new PublicGoogleSheetsParser(
  "1dL4cYaN3_5p7RGndKyIg14YcEwwcZSMedk5-QCJfMBE",
  options
);

const items = ref<{ [key: string]: string }[]>([]);

parser.parse().then((data) => {
  items.value = data.map((item, i) => {
    return {
      ...item,
      urlPhoto: item.urlPhoto,
    };
  });
});
const UAvatar = resolveComponent("UAvatar");

const router = useRouter();

const medal = (rank) => {
  const r = Number(rank);

  // if (r >= 4 && r <= 7) return "🥇"; // Gold
  if (r >= 9 && r <= 14) return "🥈"; // Silver
  if (r >= 15 && r <= 24) return "🥉"; // Bronze

  return "";
};

const columns = [
  {
    accessorKey: "rank",
    header: "Rank",
    cell: ({ row }) => {
      return h("div", {}, [
        h(
          "span",
          { class: "text-lg font-medium flex justify-center" },
          row.original.no
        ),
        // h("span", { class: "ml-2 text-lg" }, `${medal(row.original?.rank)}`),
      ]);
    },
  },
  {
    accessorKey: "urlPhoto",
    header: "Name",
    cell: ({ row }) => {
      const isSpecialRow = row.index === 8;

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
        return h(
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
                //   `${medal(row.original?.no)}`
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
      }
    },
  },
  {
    accessorKey: "scoreTotalP",
    header: "Score",
    cell: ({ row }) => {
      return h("div", {}, [
        h(
          "span",
          { class: "text-md font-medium flex justify-center" },
          row.original.scoreTotalP
        ),
        // h("span", { class: "ml-2 text-lg" }, `${medal(row.original?.rank)}`),
      ]);
    },
  },
];
</script>

<template>
  <div>
    <UTable :data="items || []" :columns="columns" />
    <UBanner class="h-[50px]" title="End of the table" />
  </div>
</template>
