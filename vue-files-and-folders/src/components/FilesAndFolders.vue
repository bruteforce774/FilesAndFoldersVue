<script setup lang="ts">
import { computed } from 'vue';
import type { FileOrFolder } from '../types';


// Define props - these come FROM the parent component
// Notice: kebab-case in HTML becomes camelCase in JavaScript
const props = defineProps<{
  items: FileOrFolder[];
  parentFolder?: number | false;
  markedFilesAndFolders: number[];
}>();

// Computed properties to separate folders and files
const folders = computed(() =>
  props.items.filter((f) => !f.hasOwnProperty('content'))
);

const files = computed(() =>
  props.items.filter((f) => f.hasOwnProperty('content'))
);

// Define events - these go TO the parent component
// The parent will listen for these with @selected and @marked-file-or-folder-changed
const emit = defineEmits<{
  selected: [id: string];  // Emits the selected file/folder ID
  markedFileOrFolderChanged: [payload: { id: number; isChecked: boolean }];
}>();

// Event handlers
function handleLinkClick(e: Event, id: number | string) {
  e.preventDefault();
  emit('selected', String(id));
}

function handleCheckboxChange(id: number, isChecked: boolean) {
  emit('markedFileOrFolderChanged', { id, isChecked });
}
</script>

<template>
  <fieldset>
    <legend>Mapper og filer</legend>

    <!-- Parent folder link (only shown if parentFolder exists) -->
    <template v-if="parentFolder">
      📁 <a href="#" @click="handleLinkClick($event, parentFolder)">..</a><br />
    </template>

    <!-- Using computed properties in template-->
    <template v-for="folder in folders" :key="folder.id">
      <input
        type="checkbox"
        :checked="markedFilesAndFolders.includes(folder.id)"
        @change="(e) => handleCheckboxChange(folder.id, (e.target as HTMLInputElement).checked)"
      />
      📁 <a href="#" @click="handleLinkClick($event, folder.id)">{{ folder.name }}</a><br />
    </template>

    <!-- Files (items WITH 'content' property) -->
    <template v-for="file in files" :key="file.id">
      <input
        type="checkbox"
        :checked="markedFilesAndFolders.includes(file.id)"
        @change="(e) => handleCheckboxChange(file.id, (e.target as HTMLInputElement).checked)"
      />
      <span>🗎</span> <a href="#" @click="handleLinkClick($event, file.id)">{{ file.name }}</a><br />
    </template>
  </fieldset>
</template>

<style scoped>
/* Scoped styles - only apply to this component */
fieldset {
  margin: 1rem 0;
  padding: 1rem;
}
</style>
