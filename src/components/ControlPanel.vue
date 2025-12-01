<template>
  <div class="control-row">
    <!-- Sorting dropdown -->
    <select v-model="localSort" @change="$emit('change-sort', localSort)">
      <option value="default">Sort by...</option>
      <option value="highToLow">Urgency: High → Low</option>
      <option value="lowToHigh">Urgency: Low → High</option>
      <option value="soonestFirst">Due Date: Soonest First</option>
      <option value="latestFirst">Due Date: Latest First</option>
    </select>

    <!-- Smart sort toggle -->
    <label class="smart-sort-toggle">
      <input
        type="checkbox"
        v-model="localSmart"
        @change="$emit('toggle-smart-sort', localSmart)"
      />
      Auto-Sort by Urgency
    </label>

    <!-- Status filter -->
    <select v-model="localFilter" @change="$emit('change-filter', localFilter)">
      <option value="all">All Tasks</option>
      <option value="completed">Completed</option>
      <option value="pending">Pending</option>
      <option value="overdue">Overdue</option>
    </select>

    <!-- Category filter -->
    <select v-model="localCategory" @change="$emit('change-category', localCategory)">
      <option value="all">All Categories</option>
      <option value="Work">Work</option>
      <option value="Personal">Personal</option>
      <option value="School">School</option>
    </select>

    <!-- Dark mode button -->
    <button @click="$emit('toggle-theme')">
      {{ darkMode ? '☀️ Light Mode' : '🌙 Dark Mode' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  darkMode: Boolean,
  sortOption: String,
  smartSort: Boolean,
  filterOption: String,
  categoryFilter: String
})

const localSort = ref(props.sortOption)
const localSmart = ref(props.smartSort)
const localFilter = ref(props.filterOption || 'all')
const localCategory = ref(props.categoryFilter || 'all')
</script>
