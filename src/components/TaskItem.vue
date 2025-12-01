<template>
  <li :class="{ overdue: isOverdue }">
    <!-- Completion checkbox -->
    <input
      type="checkbox"
      v-model="task.done"
      @change="$emit('toggle-complete')"
    />

    <!-- Task text -->
    <p>{{ task.text }}</p>

    <!-- Urgency badge -->
    <span :class="['urgency', task.urgency.toLowerCase()]">
      {{ task.urgency }}
    </span>

    <!-- Due date -->
    <span class="due-date">
      📅 {{ formattedDate }}
    </span>

    <!-- Category label -->
    <span class="category-label">
      🗂 {{ task.category || 'Uncategorized' }}
    </span>

    <!-- Action buttons -->
    <button class="btn editBtn" @click="$emit('edit-task')">Edit</button>
    <button class="btn deleteBtn" @click="$emit('delete-task')">Delete</button>
    <button class="btn detailsBtn" @click="$emit('show-details', task)">Details</button>
  </li>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  task: Object
})

const formattedDate = computed(() => {
  if (!props.task.dueDate) return 'No deadline'
  const date = new Date(props.task.dueDate)
  return date.toLocaleDateString()
})

const isOverdue = computed(() => {
  if (!props.task.dueDate || props.task.done) return false
  const today = new Date()
  return new Date(props.task.dueDate) < today
})
</script>

<style scoped>
.category-label {
  margin-left: 10px;
  font-weight: bold;
  color: #4b5563; /* neutral gray */
}

.overdue {
  background-color: #ffe5e5;
  border-left: 4px solid #ff4d4d;
}

.detailsBtn {
  background-color: #2563eb; /* blue */
  color: #fff;
  margin-left: 5px;
}
</style>
