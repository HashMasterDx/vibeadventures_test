<template>
  <BaseHeader></BaseHeader>
  <main>
    <slot />
  </main>
</template>
<script setup lang="ts">
import BaseHeader from "~/components/BaseHeader.vue";
</script>