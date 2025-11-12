<script setup lang="ts">
import type { NavigationMenuItem } from "@nuxt/ui";

const showModal = ref(false);
const items = computed<NavigationMenuItem[]>(() => [
  {
    label: "Schedule",
    click: () => (showModal.value = true),
    icon: "i-lucide-book-open",
  },
]);

const schedule = [
  {
    day: "Saturday",
    events: [
      { time: "08.00", activity: "Registration open" },
      { time: "09.00", activity: "Opening Ceremony" },
      { time: "10.00", activity: "Keynote Speech" },
      { time: "11.00", activity: "Workshop Session" },
    ],
  },
  {
    day: "Sunday",
    events: [
      { time: "08.00", activity: "Breakfast & Networking" },
      { time: "09.30", activity: "Panel Discussion" },
      { time: "11.00", activity: "Closing Remarks" },
    ],
  },
];
</script>

<template>
  <UHeader title="IIRC 2025" mode="slideover">
    <template #body>
      <UModal>
        <UNavigationMenu
          :items="items"
          orientation="vertical"
          class="-mx-2.5"
        />
        <template #content>
          <UCard>
            <template #header>Schedule</template>
            <div v-for="(day, i) in schedule" :key="i" class="space-y-3">
              <p class="font-semibold">{{ day.day }}</p>
              <USeparator />

              <div class="grid grid-cols-3 gap-2 items-center">
                <template v-for="(event, j) in day.events" :key="j">
                  <p>{{ event.time }}</p>
                  <USeparator orientation="vertical" class="h-4 mx-auto" />
                  <p>{{ event.activity }}</p>
                </template>
              </div>

              <USeparator v-if="i < schedule.length - 1" class="my-2" />
            </div>
          </UCard>
        </template>
      </UModal>
    </template>
  </UHeader>
</template>
