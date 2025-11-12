<script setup lang="ts">
import { ref } from 'vue';

// Component's internal state
const name = ref('');
const errorMessage = ref('');

// Define the event this component emits
const emit = defineEmits<{
  contentAdded: [payload: { isFile: boolean; name: string }];
}>();

// Handle button click - add file or folder
function handleClick(isFile: boolean) {
  // Validate that name is not empty
  if (!name.value || name.value.length === 0) {
    errorMessage.value = 'Du må skrive inn et navn!';
    return;
  }

  // Emit the event with the data
  emit('contentAdded', {
    isFile,
    name: name.value,
  });

  // Clear the form after successful submission
  name.value = '';
  errorMessage.value = '';
}
</script>

<template>
  <fieldset>
    <legend>Legg til fil eller mappe</legend>

    <!-- Error message -->
    <div v-if="errorMessage" style="color: red">{{ errorMessage }}</div>

    <!-- Input with v-model for two-way binding -->
    <input
      v-model="name"
      type="text"
      placeholder="Skriv inn navn..."
      @keyup.enter="handleClick(true)"
    />

    <button @click="handleClick(true)">Ny fil</button>
    <button @click="handleClick(false)">Ny mappe</button>
  </fieldset>
</template>

<style scoped>
fieldset {
  margin: 1rem 0;
  padding: 1rem;
}

input {
  margin-right: 0.5rem;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  min-width: 200px;
}

button {
  margin-right: 0.5rem;
  padding: 0.5rem 1rem;
  cursor: pointer;
  border: 1px solid #ccc;
  border-radius: 4px;
  background: white;
}

button:hover {
  background: #e0e0e0;
}

div[style*="color: red"] {
  margin-bottom: 0.5rem;
  font-weight: bold;
}
</style>
