<script setup lang="ts">
import draggable from "vuedraggable";
import {nanoid} from "nanoid";
import type {Column, Task} from "~~/types";

const columns = useLocalStorage<Column[]>("trelloBoard", [
  {
    title: "Backlog",
    id: nanoid(),
    tasks: [
      {
        title: "Create marketing landing page",
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: "Develop cool new feature",
        createdAt: new Date(),
        id: nanoid(),
      },
      {title: "Fix page nav bug", createdAt: new Date(), id: nanoid()},
    ],
  },
  {title: "Selected for Dev", id: nanoid(), tasks: []},
  {title: "In Progress", id: nanoid(), tasks: []},
  {title: "QA", id: nanoid(), tasks: []},
  {title: "Complete", id: nanoid(), tasks: []},
]);

const alt = useKeyModifier("Alt");

const createColumn = () => {
  const column: Column = {
    title: "",
    id: nanoid(),
    tasks: [],
  };

  columns.value.push(column);

  nextTick(() => {
    (
      document.querySelector(
        ".column:last-of-type .title-input"
      ) as HTMLInputElement
    ).focus();
  });
};
</script>
<template>
  <div class="flex gap-4 items-start overflow-x-auto">
    <draggable
      v-model="columns"
      group="columns"
      :animation="150"
      handle=".drag-handle"
      item-key="id"
      class="flex gap-4 items-start"
    >
      <template #item="{element: column}: {element: Column}">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <DragHandle />
            <input
              v-model="column.title"
              type="text"
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? columns.splice(columns.indexOf(column), 1)
                  : null
              "
            />
          </header>
          <draggable
            v-model="column.tasks"
            :group="{name: 'tasks', pull: alt ? 'clone' : true, put: true}"
            :animation="150"
            handle=".drag-handle"
            item-key="id"
          >
            <template #item="{element: task}: {element: Task}">
              <div>
                <TrelloBoardTask
                  :task="task"
                  @delete="
                    column.tasks = column.tasks.filter((i) => i.id !== $event)
                  "
                />
              </div>
            </template>
          </draggable>
          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
      @click="createColumn"
    >
      + Add Another Column
    </button>
  </div>
</template>
