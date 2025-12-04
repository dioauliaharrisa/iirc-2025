<template>
  <header
    class="mt-auto bg-primary fixed bottom-0 left-0 w-full z-50 flex items-center justify-between px-4 pt-1.5"
  >
    <div>
      <UIcon
        name="i-material-symbols:home"
        class="size-10 text-white"
        @click="$router.push('/')"
      />
    </div>
  </header>
</template>
