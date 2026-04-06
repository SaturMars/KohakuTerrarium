<template>
  <div class="h-full flex flex-col bg-warm-50 dark:bg-warm-900 border-t border-warm-200 dark:border-warm-700">
    <!-- Status bar -->
    <div class="flex items-center gap-3 px-3 py-2 text-xs shrink-0 border-b border-warm-200 dark:border-warm-700">
      <span class="text-warm-400 font-mono truncate flex-1">
        {{ editor.activeFilePath || "No file open" }}
      </span>
      <span
        v-if="editor.activeFile"
        class="px-1.5 py-0.5 rounded text-[10px] font-mono"
        :class="editor.activeFile.dirty
          ? 'bg-amber/15 text-amber'
          : 'bg-aquamarine/15 text-aquamarine'"
      >
        {{ editor.activeFile.dirty ? "Unsaved" : "Saved" }}
      </span>
    </div>

    <!-- Info + actions -->
    <div class="flex-1 overflow-y-auto p-3">
      <div v-if="editor.activeFile" class="flex flex-col gap-2">
        <!-- Language -->
        <div class="flex items-center gap-2 text-xs">
          <span class="text-warm-400 w-16">Language</span>
          <span class="text-warm-600 dark:text-warm-300 font-mono">
            {{ editor.activeFile.language || "plain" }}
          </span>
        </div>

        <!-- Lines -->
        <div class="flex items-center gap-2 text-xs">
          <span class="text-warm-400 w-16">Lines</span>
          <span class="text-warm-600 dark:text-warm-300 font-mono">
            {{ lineCount }}
          </span>
        </div>

        <!-- Size -->
        <div class="flex items-center gap-2 text-xs">
          <span class="text-warm-400 w-16">Size</span>
          <span class="text-warm-600 dark:text-warm-300 font-mono">
            {{ formatSize(editor.activeFile.content.length) }}
          </span>
        </div>

        <!-- Working dir -->
        <div class="flex items-center gap-2 text-xs mt-2">
          <span class="text-warm-400 w-16">CWD</span>
          <span class="text-warm-600 dark:text-warm-300 font-mono truncate">
            {{ editor.treeRoot }}
          </span>
        </div>

        <!-- Actions -->
        <div class="flex gap-2 mt-3">
          <button
            class="px-3 py-1.5 rounded-md text-xs font-medium transition-colors"
            :class="editor.activeFile.dirty
              ? 'bg-iolite text-white hover:bg-iolite-shadow'
              : 'bg-warm-200 dark:bg-warm-700 text-warm-400 cursor-not-allowed'"
            :disabled="!editor.activeFile.dirty"
            @click="save"
          >
            Save
          </button>
          <button
            class="px-3 py-1.5 rounded-md text-xs font-medium bg-warm-200 dark:bg-warm-700 text-warm-600 dark:text-warm-300 hover:bg-warm-300 dark:hover:bg-warm-600 transition-colors"
            @click="revert"
          >
            Revert
          </button>
        </div>
      </div>

      <div v-else class="text-xs text-warm-400 py-4 text-center">
        Select a file to view details
      </div>
    </div>
  </div>
</template>

<script setup>
import { useEditorStore } from "@/stores/editor";

const editor = useEditorStore();

const lineCount = computed(() => {
  if (!editor.activeFile) return 0;
  return editor.activeFile.content.split("\n").length;
});

function formatSize(bytes) {
  if (bytes < 1024) return bytes + " B";
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(1) + " KB";
  return (bytes / (1024 * 1024)).toFixed(1) + " MB";
}

function save() {
  if (editor.activeFilePath) {
    editor.saveFile(editor.activeFilePath);
  }
}

function revert() {
  if (editor.activeFilePath) {
    editor.revertFile(editor.activeFilePath);
  }
}
</script>
