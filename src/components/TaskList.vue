<template>
  <ul>
    <TaskItem
      v-for="(task, index) in tasks"
      :key="index"
      :task="task"
      @delete-task="$emit('delete-task', index)"
      @edit-task="$emit('edit-task', index)"
      @toggle-complete="$emit('toggle-complete', index)"
      @show-details="$emit('show-details', task)" 
    />
  </ul>
</template>

<script setup>
import TaskItem from './TaskItem.vue'

const props = defineProps({
  tasks: Array
})

const emit = defineEmits([
  'delete-task',
  'edit-task',
  'toggle-complete',
  'show-details'
])
</script>
