<template>
    <div class="task-item">
      <input 
        type="checkbox" 
        v-model="task.completed" 
        @change="updateStatus"
      />
      <span :class="{ 'completed': task.completed }">{{ task.name }}</span>
      <button @click="openEditModal">✏️</button>
      <button @click="$emit('delete-task', task.id)">🗑️</button>
      
      <EditTaskModal 
        v-if="showEditModal" 
        :task="task" 
        @close="showEditModal = false" 
        @update="updateTask"
      />
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  import EditTaskModal from './EditTaskModal.vue';
  
  const props = defineProps(['task']);
  const emit = defineEmits(['delete-task']);
  const showEditModal = ref(false);
  
  const updateStatus = async () => {
    await axios.patch(`http://localhost:3000/tasks/${task.id}`, {
      completed: task.completed,
    });
  };
  
  const openEditModal = () => {
    showEditModal.value = true;
  };
  
  const updateTask = (updatedTask) => {
    emit('update-task', updatedTask);
    showEditModal.value = false;
  };
  </script>
  
  <style scoped>
  .task-item {
    display: flex;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid #ddd;
  }
  
  .completed {
    text-decoration: line-through;
    color: gray;
  }
  </style>