<script setup lang="ts">
import type {ID, Task} from "@/types";
const props = defineProps<{
  task: Task;
}>();

const emit = defineEmits<{
  (e: "delete", payload: ID): void;
}>();

const focused = ref(false);

onKeyStroke("Backspace", () => {
  if (focused.value) emit("delete", props.task.id);
});
</script>
<template>
  <div
    :title="task.createdAt.toLocaleString()"
    class="task bg-white p-2 mb-2 rounded shadow-sm max-w-[250px] flex gap-2 focus-within:bg-gray-100"
    tabindex="0"
    @focus="focused = true"
    @blur="focused = false"
  >
    <DragHandle />
    <span>
      {{ task.title }}
    </span>
  </div>
</template>
<style scoped>
.sortable-drag .task {
  transform: rotate(5deg);
}

.sortable-ghost .task {
  position: relative;
}

.sortable-ghost .task::after {
  content: "";
  @apply absolute top-0 bottom-0 left-0 right-0 bg-slate-300 rounded;
}
</style>
