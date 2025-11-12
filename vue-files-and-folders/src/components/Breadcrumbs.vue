<script setup lang="ts">
import { computed } from 'vue';

// Define props
const props = defineProps<{
  texts: string[];
}>();

// Computed property for the breadcrumb display
// This keeps the template clean and logic reusable
const breadcrumbText = computed(() => {
  if (!props.texts || props.texts.length === 0) {
    return null; // Will show "rotmappe" instead
  }
  return ' > ' + props.texts.join(' > ');
});

// We could also just check props.texts.length in the template,
// but computed makes it more semantic
const isRoot = computed(() => !props.texts || props.texts.length === 0);
</script>

<template>
  <fieldset>
    <legend>Her er du nå</legend>

    <!-- Using v-if/v-else for conditional rendering -->
    <i v-if="isRoot">rotmappe</i>
    <span v-else>{{ breadcrumbText }}</span>
  </fieldset>
</template>

<style scoped>
fieldset {
  margin: 1rem 0;
  padding: 1rem;
}

legend {
  font-weight: bold;
}
</style>
