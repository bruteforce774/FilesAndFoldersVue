<script setup lang="ts">
import { ref } from 'vue';

// Internal state - this component manages its own UI state
// Using ref() because it's a single boolean primitive
const isInInitialPhase = ref(true);

// Define the event this component can emit
const emit = defineEmits<{
  deleteFileOrFolder: [];  // No payload needed
}>();

// Handle phase change (show/hide confirmation)
function handleChangePhase(newPhase: boolean) {
  isInInitialPhase.value = newPhase;
}

// Handle the actual delete action
function handleDelete() {
  emit('deleteFileOrFolder');
}
</script>

<template>
  <fieldset>
    <legend>Slett fil eller mappe</legend>

    <!-- Phase 1: Initial state - just a delete button -->
    <div v-if="isInInitialPhase">
      <button @click="handleChangePhase(false)">Slett</button>
    </div>

    <!-- Phase 2: Confirmation state - confirm or cancel -->
    <div v-else>
      Er du sikker på at du vil slette?<br/>
      <button @click="handleDelete">Ja, slett!</button>
      <button @click="handleChangePhase(true)">Avbryt</button>
    </div>
  </fieldset>
</template>

<style scoped>
fieldset {
  margin: 1rem 0;
  padding: 1rem;
}

button {
  margin-right: 0.5rem;
  padding: 0.5rem 1rem;
  cursor: pointer;
}

button:hover {
  background: #e0e0e0;
}
</style>
