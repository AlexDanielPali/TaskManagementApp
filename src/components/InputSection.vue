<template>
  <div class="input-container">
    <h1>My To-Do List</h1>

    <!-- Task input field -->
    <input
      v-model="taskText"
      type="text"
      placeholder="Add a new task..."
    />

    <!-- Urgency selector -->
    <select v-model="urgency">
      <option value="Low">Low</option>
      <option value="Medium">Medium</option>
      <option value="High">High</option>
    </select>

    <!-- Due date picker -->
    <input
      v-model="dueDate"
      type="date"
    />

    <!-- Category selector -->
    <select v-model="category">
      <option value="Work">Work</option>
      <option value="Personal">Personal</option>
      <option value="School">School</option>
    </select>

    <!-- Add/Edit button -->
    <button @click="emitTask">
      {{ editMode ? 'Edit' : 'Add' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  editMode: Boolean,
  editTask: Object
})

const emit = defineEmits(['add-task'])

const taskText = ref('')
const urgency = ref('Medium')
const dueDate = ref('')
const category = ref('Work')

function emitTask() {
  if (!taskText.value.trim()) {
    alert('You must write something!')
    return
  }
  emit('add-task', {
    text: taskText.value,
    urgency: urgency.value,
    dueDate: dueDate.value,
    category: category.value
  })
  taskText.value = ''
  dueDate.value = ''
  category.value = 'Work'
}
</script>
