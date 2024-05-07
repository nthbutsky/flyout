<template>
  <span class="font-2-header text-color-1"> FLYOUT </span>
  <div class="p-4 bg-neutral-150 border-dashed border-2 border-color-1 rounded-lg w-full">
    <flyout
      body-offset="80px"
      :align="'BOTTOM_CENTER'"
    >
      <template #trigger="{ toggle }">
        <button-text
          color="BLUE"
          text="Open the flyout"
          class="relative"
          @click="toggle"
        />
      </template>
      <template #body>
        <ul class="flex flex-col gap-[14px]">
          <li>flyout item 1</li>
          <li>flyout item 2</li>
        </ul>
      </template>
    </flyout>
  </div>
</template>

<script setup lang="ts">
import Flyout from './flyout.vue';
import ButtonText from '@/components/button-text/button-text.vue';
</script>
