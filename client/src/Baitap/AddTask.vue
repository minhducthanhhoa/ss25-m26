<template>
    <div class="add-task">
      <input 
        type="text" 
        v-model="taskName" 
        @focus="clearError" 
        placeholder="Nhập tên công việc" 
        :class="{ 'input-error': error }"
      />
      <button @click="addTask">Thêm công việc</button>
      <div v-if="error" class="error-message">{{ error }}</div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  import { defineEmits } from 'vue';
  
  const emit = defineEmits(['add-task']);
  const taskName = ref('');
  const error = ref('');
  
  const addTask = async () => {
    if (!taskName.value.trim()) {
      error.value = 'Tên công việc không được phép để trống';
      return;
    }
  
    const existingTasks = await fetchTasks();
    if (existingTasks.some(task => task.name === taskName.value)) {
      error.value = 'Tên công việc không được phép trùng';
      return;
    }
  
    const newTask = { name: taskName.value, status: 'pending' };
    await axios.post('http://localhost:3000/tasks', newTask);
    emit('add-task', newTask);
    
    taskName.value = '';
  };
  
  const clearError = () => {
    error.value = '';
  };
  
  const fetchTasks = async () => {
    const response = await axios.get('http://localhost:3000/tasks');
    return response.data;
  };
  </script>
  
  <style scoped>
  .add-task {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }
  
  .input-error {
    border: 1px solid red;
  }
  
  .error-message {
    color: red;
    font-size: 12px;
  }
  </style>