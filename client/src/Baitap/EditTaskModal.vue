<template>
    <div class="modal">
      <div class="modal-content">
        <h3>Sửa công việc</h3>
        <input 
          type="text" 
          v-model="taskName" 
          @focus="clearError" 
          placeholder="Nhập tên công việc" 
          :class="{ 'input-error': error }"
        />
        <div v-if="error" class="error-message">{{ error }}</div>
        <button @click="updateTask">Cập nhật</button>
        <button @click="$emit('close')">Hủy</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, defineProps, defineEmits } from 'vue';
  import axios from 'axios';
  
  const props = defineProps(['task']);
  const emit = defineEmits(['close', 'update']);
  
  const taskName = ref(props.task.name);
  const error = ref('');
  
  const updateTask = async () => {
    if (!taskName.value.trim()) {
      error.value = 'Tên công việc không được phép để trống';
      return;
    }
  
    const existingTasks = await fetchTasks();
    if (existingTasks.some(task => task.name === taskName.value && task.id !== props.task.id)) {
      error.value = 'Tên công việc không được phép trùng';
      return;
    }
  
    await axios.patch(`http://localhost:3000/tasks/${props.task.id}`, {
      name: taskName.value,
    });
  
    emit('update', { ...props.task, name: taskName.value });
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
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .modal-content {
    background-color: white;
    padding: 20px;
    border-radius: 5px;
    text-align: center;
  }
  
  .input-error {
    border: 1px solid red;
  }
  
  .error-message {
    color: red;
    font-size: 12px;
  }
  </style>