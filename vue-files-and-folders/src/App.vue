<script setup lang="ts">
import { reactive, computed } from 'vue';
import FilesAndFolders from './components/FilesAndFolders.vue';
import Breadcrumbs from './components/Breadcrumbs.vue';
import DeleteFileOrFolder from './components/DeleteFileOrFolder.vue';
import AddFileOrFolder from './components/AddFileOrFolder.vue';
import type { FileOrFolder } from './types';

// Reactive state - Vue automatically tracks changes!
const state = reactive({
  currentId: undefined as number | undefined,
  filesAndFolders: [
    { id: 1, name: "Handlelister" },
    { id: 2, name: "Ting som skal fikses" },
    { id: 3, name: "Oktober", parentId: 1 },
    { id: 4, name: "Tirsdag 15.", parentId: 3, content: "melk\nbrød\nost\n" },
    { id: 5, name: "Bad", parentId: 2, content: "Lekkasje, bla bla" },
    { id: 6, name: "notater.txt", content: "abc" },
  ] as FileOrFolder[],
  markedFilesAndFolders: new Set<number>(),
});

// Computed: Get the current file or folder
const currentFileOrFolder = computed(() =>
  state.filesAndFolders.find(f => f.id === state.currentId)
);

// Computed: Get items to display (children of current folder, or root items)
const currentItems = computed(() =>
  state.filesAndFolders.filter(f => f.parentId === state.currentId)
);

// Computed: Get parent folder ID for the ".." link
const parentFolderId = computed(() => {
  if (!currentFileOrFolder.value) return false;
  return currentFileOrFolder.value.parentId ?? -1;
});

// Computed: Convert Set to Array for the component
const markedFilesArray = computed(() => Array.from(state.markedFilesAndFolders));

// Computed: Build breadcrumb trail by walking up the parent chain
const breadcrumbTexts = computed(() => {
  if (!state.currentId) return [];

  const breadcrumbs: string[] = [];
  let id = state.currentId;

  // Walk up the parent chain
  while (id) {
    const fileOrFolder = state.filesAndFolders.find(f => f.id === id);
    if (!fileOrFolder) break;
    breadcrumbs.push(fileOrFolder.name);
    id = fileOrFolder.parentId;
  }

  // Reverse so it goes from root to current
  return breadcrumbs.reverse();
});

// Computed: Check if there are any marked items
const hasMarkedItems = computed(() => state.markedFilesAndFolders.size > 0);

// Event handler: Navigate to selected folder/file
function handleSelected(id: string) {
  if (id === '-1') {
    // Go to root
    state.currentId = undefined;
  } else {
    // Navigate into folder or select file
    state.currentId = parseInt(id);
  }
  // No scheduleRender() needed! Vue automatically updates the UI
}

// Event handler: Mark/unmark files and folders
function handleMarkedFileOrFolderChanged(payload: { id: number; isChecked: boolean }) {
  if (payload.isChecked) {
    state.markedFilesAndFolders.add(payload.id);
  } else {
    state.markedFilesAndFolders.delete(payload.id);
  }
  // Again, no manual render! Vue's reactivity handles it
}

// Event handler: Delete marked files and folders
function handleDelete() {
  // Filter out all marked items
  state.filesAndFolders = state.filesAndFolders.filter(
    f => !state.markedFilesAndFolders.has(f.id)
  );
  // Clear the marked items set
  state.markedFilesAndFolders.clear();
  // Vue automatically re-renders!
}

// Event handler: Add new file or folder
function handleContentAdded(payload: { isFile: boolean; name: string }) {
  // Generate new ID (max existing ID + 1)
  const newId = Math.max(...state.filesAndFolders.map(f => f.id)) + 1;

  // Create new file or folder
  const newContent: FileOrFolder = {
    id: newId,
    name: payload.name,
  };

  // Determine parent: if we're in a file, use its parent; if in folder, use folder itself
  const current = currentFileOrFolder.value;
  if (current) {
    newContent.parentId = current.hasOwnProperty('content')
      ? current.parentId
      : current.id;
  }

  // If it's a file, add empty content property
  if (payload.isFile) {
    newContent.content = '';
  }

  // Add to state - Vue automatically updates!
  state.filesAndFolders.push(newContent);
}
</script>

<template>
  <div class="app">
    <h1>Files and Folders - Vue Edition</h1>

    <!-- Breadcrumbs component -->
    <Breadcrumbs :texts="breadcrumbTexts" />

    <!-- Debug info (we'll remove this later) -->
    <div class="debug">
      <p>Current ID: {{ state.currentId ?? 'root' }}</p>
      <p>Current folder: {{ currentFileOrFolder?.name ?? 'Root' }}</p>
      <p>Items showing: {{ currentItems.length }}</p>
      <p>Marked items: {{ state.markedFilesAndFolders.size }}</p>
    </div>

    <!-- FilesAndFolders component -->
    <FilesAndFolders
      :items="currentItems"
      :parent-folder="parentFolderId"
      :marked-files-and-folders="markedFilesArray"
      @selected="handleSelected"
      @marked-file-or-folder-changed="handleMarkedFileOrFolderChanged"
    />

    <!-- AddFileOrFolder component -->
    <AddFileOrFolder @content-added="handleContentAdded" />

    <!-- DeleteFileOrFolder - only shown when items are marked -->
    <DeleteFileOrFolder
      v-if="hasMarkedItems"
      @delete-file-or-folder="handleDelete"
    />
  </div>
</template>

<style>
/* Global styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: system-ui, -apple-system, sans-serif;
  padding: 2rem;
  background: #f5f5f5;
}

.app {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

h1 {
  margin-bottom: 1rem;
  color: #333;
}

.debug {
  background: #f0f0f0;
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 4px;
  font-size: 0.9rem;
  color: #666;
}

.debug p {
  margin: 0.25rem 0;
}
</style>
