<script setup lang="ts"></script>

<template>
  <UHeader
    :ui="{
      root: 'mt-auto bg-primary sticky bottom-0 z-50 ',
      toggle: 'hidden',
    }"
  >
    <template #left><div>666</div></template>

    <template #right>
      <UIcon
        name="i-material-symbols:home"
        class="size-12 text-white"
        @click="$router.push('/')"
      />
    </template>
  </UHeader>
</template>
