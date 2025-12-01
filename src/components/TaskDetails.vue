<template>
  <transition name="slide">
    <div v-if="visible" class="side-panel-overlay">
      <div class="side-panel">
        <!-- Header bar -->
        <div class="panel-header">
          <h2>Task Details</h2>
          <button class="close-btn" @click="$emit('close')">✖</button>
        </div>

        <!-- Editable fields -->
        <div class="panel-content">
          <label>Text:</label>
          <input v-model="localTask.text" type="text" />

          <label>Urgency:</label>
          <select v-model="localTask.urgency">
            <option value="Low">Low</option>
            <option value="Medium">Medium</option>
            <option value="High">High</option>
          </select>

          <label>Due Date:</label>
          <input v-model="localTask.dueDate" type="date" />

          <label>Category:</label>
          <select v-model="localTask.category">
            <option value="Work">Work</option>
            <option value="Personal">Personal</option>
            <option value="School">School</option>
          </select>

          <label>Notes:</label>
          <textarea v-model="localTask.notes" placeholder="Add notes..."></textarea>

          <!-- Mark complete toggle -->
          <label>
            <input type="checkbox" v-model="localTask.done" />
            Mark as Completed
          </label>
        </div>

        <!-- Footer actions -->
        <div class="panel-actions">
          <button class="save-btn" @click="saveChanges">Save</button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  task: Object,
  visible: Boolean
})

const emit = defineEmits(['close', 'update-task'])

const localTask = ref({ ...props.task })

watch(() => props.task, (newTask) => {
  if (newTask) localTask.value = { ...newTask }
})

function saveChanges() {
  emit('update-task', localTask.value)
  emit('close')
}
</script>

<style scoped>
.side-panel-overlay {
  position: fixed;
  top: 0; right: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.4);
}

.side-panel {
  position: absolute;
  top: 0; right: 0;
  width: 380px;
  height: 100%;
  background: var(--bg, #fff);
  color: var(--fg, #111);
  padding: 0;
  box-shadow: -2px 0 8px rgba(0,0,0,0.2);
  display: flex;
  flex-direction: column;
}

/* Header bar */
.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: var(--btn-bg, #f3f4f6);
  padding: 12px 16px;
  border-bottom: 1px solid #ddd;
}
.close-btn {
  background: transparent;
  border: none;
  font-size: 18px;
  cursor: pointer;
}

/* Content */
.panel-content {
  flex: 1;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.panel-content input,
.panel-content select,
.panel-content textarea {
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

/* Footer */
.panel-actions {
  padding: 12px 16px;
  border-top: 1px solid #ddd;
  text-align: right;
}
.save-btn {
  background: #2563eb;
  color: #fff;
  padding: 8px 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

/* Slide animation */
.slide-enter-active, .slide-leave-active {
  transition: transform 0.3s ease;
}
.slide-enter-from, .slide-leave-to {
  transform: translateX(100%);
}
.slide-enter-to, .slide-leave-from {
  transform: translateX(0);
}
</style>
