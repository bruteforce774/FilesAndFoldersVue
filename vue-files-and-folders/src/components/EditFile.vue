<script setup lang="ts">
import { ref, watch } from 'vue';

// Props received from parent
const props = defineProps<{
  content: string;
  id: number;
}>();

// Local state for editing (using v-model)
// Initialize with the prop value
const editableContent = ref(props.content);

// Watch for prop changes (if user switches to a different file)
watch(() => props.content, (newContent) => {
  editableContent.value = newContent;
});

// Define events
const emit = defineEmits<{
  contentSaved: [payload: { content: string; id: number }];
  editCancelled: [payload: { id: number }];
}>();

// Save handler
function handleSave() {
  emit('contentSaved', {
    content: editableContent.value,
    id: props.id,
  });
}

// Cancel handler
function handleCancel() {
  emit('editCancelled', {
    id: props.id,
  });
}
</script>

<template>
  <fieldset>
    <legend>Rediger fil</legend>

    <!-- Textarea with v-model for two-way binding -->
    <textarea
      v-model="editableContent"
      rows="10"
      placeholder="Skriv filinnhold her..."
    ></textarea>

    <div class="button-group">
      <button @click="handleSave">Lagre</button>
      <button @click="handleCancel">Avbryt</button>
    </div>
  </fieldset>
</template>

<style scoped>
fieldset {
  margin: 1rem 0;
  padding: 1rem;
}

textarea {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  resize: vertical;
  min-height: 200px;
}

.button-group {
  margin-top: 0.5rem;
  display: flex;
  gap: 0.5rem;
}

button {
  padding: 0.5rem 1rem;
  cursor: pointer;
  border: 1px solid #ccc;
  border-radius: 4px;
  background: white;
}

button:hover {
  background: #e0e0e0;
}

button:first-child {
  background: #4CAF50;
  color: white;
  border-color: #4CAF50;
}

button:first-child:hover {
  background: #45a049;
}
</style>
