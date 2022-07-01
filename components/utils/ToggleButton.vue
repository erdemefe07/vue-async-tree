<template>
  <span v-if="isExpanded" @click.stop="collapse(model)">
    <down />
  </span>
  <span v-else @click.stop="expand(model)">
    <right />
  </span>
</template>

<script setup>
import down from "./down.vue";
import right from "./right.vue";

const props = defineProps(["isExpanded", "expand", "collapse", "model"]);
</script>

<style scoped>
</style>