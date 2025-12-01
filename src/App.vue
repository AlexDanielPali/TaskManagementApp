<template>
  <div class="container">
    <InputSection
      :editMode="editMode"
      :editTask="editTask"
      @add-task="addTask"
    />

    <ControlPanel
      :darkMode="darkMode"
      :sortOption="sortOption"
      :smartSort="smartSort"
      :filterOption="filterOption"
      :categoryFilter="categoryFilter"
      @toggle-theme="toggleTheme"
      @change-sort="sortOption = $event"
      @toggle-smart-sort="smartSort = $event"
      @change-filter="filterOption = $event"
      @change-category="categoryFilter = $event"
    />

    <TaskList
      :tasks="filteredTasks"
      @delete-task="deleteTask"
      @edit-task="startEdit"
      @toggle-complete="saveTasks"
      @show-details="openDetails"
    />

    <h2>You have done {{ completedCount }} out of {{ tasks.length }} tasks!</h2>
    <p>{{ quote }}</p>

    <!-- Side panel for task details -->
    <TaskDetails
      v-if="showDetails"
      :task="selectedTask"
      :visible="showDetails"
      @close="closeDetails"
      @update-task="updateTask"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import InputSection from './components/InputSection.vue'
import ControlPanel from './components/ControlPanel.vue'
import TaskList from './components/TaskList.vue'
import TaskDetails from './components/TaskDetails.vue'

const tasks = ref([])
const sortOption = ref('default')
const smartSort = ref(true)
const filterOption = ref('all')
const categoryFilter = ref('all')
const darkMode = ref(false)
const editMode = ref(false)
const editTask = ref(null)

const selectedTask = ref(null)
const showDetails = ref(false)

function openDetails(task) {
  selectedTask.value = task
  showDetails.value = true
}

function closeDetails() {
  showDetails.value = false
  selectedTask.value = null
}

function updateTask(updatedTask) {
  const index = tasks.value.findIndex(t => t === selectedTask.value)
  if (index !== -1) {
    tasks.value[index] = { ...updatedTask }
    saveTasks()
  }
}

const quotes = [
  "Keep going, you’re doing great!",
  "Progress, not perfection!",
  "Small steps every day!",
  "Stay focused and crush it today!",
  "Dream big, work hard, stay humble!"
]
const quote = ref(quotes[Math.floor(Math.random() * quotes.length)])

function addTask(task) {
  if (editMode.value) {
    tasks.value[editTask.value].text = task.text
    tasks.value[editTask.value].urgency = task.urgency
    tasks.value[editTask.value].dueDate = task.dueDate
    tasks.value[editTask.value].category = task.category
    tasks.value[editTask.value].notes = task.notes || ''
    editMode.value = false
    editTask.value = null
  } else {
    tasks.value.push({ ...task, done: false, notes: task.notes || '' })
  }
  saveTasks()
}

function deleteTask(index) {
  tasks.value.splice(index, 1)
  saveTasks()
}

function startEdit(index) {
  editMode.value = true
  editTask.value = index
}

function saveTasks() {
  localStorage.setItem('todos', JSON.stringify(tasks.value))
  if (tasks.value.length && tasks.value.every(t => t.done)) {
    alert('🎉 All tasks completed!')
  }
}

function toggleTheme() {
  darkMode.value = !darkMode.value
  localStorage.setItem('theme', darkMode.value ? 'dark' : 'light')
  document.body.classList.toggle('dark', darkMode.value)
}

const completedCount = computed(() => tasks.value.filter(t => t.done).length)

function urgencyRank(level) {
  return { High: 3, Medium: 2, Low: 1 }[level] || 0
}

const sortedTasks = computed(() => {
  let list = [...tasks.value]

  if (smartSort.value || sortOption.value === 'highToLow') {
    return list.sort((a, b) => urgencyRank(b.urgency) - urgencyRank(a.urgency))
  }
  if (sortOption.value === 'lowToHigh') {
    return list.sort((a, b) => urgencyRank(a.urgency) - urgencyRank(b.urgency))
  }

  if (sortOption.value === 'soonestFirst') {
    return list.sort((a, b) => {
      if (!a.dueDate) return 1
      if (!b.dueDate) return -1
      return new Date(a.dueDate) - new Date(b.dueDate)
    })
  }
  if (sortOption.value === 'latestFirst') {
    return list.sort((a, b) => {
      if (!a.dueDate) return 1
      if (!b.dueDate) return -1
      return new Date(b.dueDate) - new Date(a.dueDate)
    })
  }

  return list
})

const filteredTasks = computed(() => {
  let list = [...sortedTasks.value]

  if (filterOption.value === 'completed') {
    list = list.filter(t => t.done)
  } else if (filterOption.value === 'pending') {
    list = list.filter(t => !t.done)
  } else if (filterOption.value === 'overdue') {
    list = list.filter(t => t.dueDate && !t.done && new Date(t.dueDate) < new Date())
  }

  if (categoryFilter.value !== 'all') {
    list = list.filter(t => t.category === categoryFilter.value)
  }

  return list
})

onMounted(() => {
  tasks.value = JSON.parse(localStorage.getItem('todos')) || []
  darkMode.value = localStorage.getItem('theme') === 'dark'
  document.body.classList.toggle('dark', darkMode.value)
})

watch(tasks, saveTasks, { deep: true })
</script>
