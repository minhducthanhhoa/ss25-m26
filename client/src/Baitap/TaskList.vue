<template>
    <div class="task-list">
      <h2>Quản lý công việc</h2>
      <AddTask @add-task="addTask" />
      <div class="tasks">
        <div v-if="loading" class="loading">Đang tải dữ liệu...</div>
        <div v-if="!loading && tasks.length === 0">Không có công việc nào.</div>
        <div class="task-container" v-if="!loading">
          <TaskItem
            v-for="task in tasks"
            :key="task.id"
            :task="task"
            @delete-task="deleteTask"
            @update-task="updateTask"
          />
        </div>
      </div>
      <button @click="deleteCompletedTasks">Xóa công việc hoàn thành</button>
      <button @click="deleteAllTasks">Xóa tất cả công việc</button>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import TaskItem from './TaskItem.vue';
  import AddTask from './AddTask.vue';
  import axios from 'axios';
  
  const tasks = ref([]);
  const loading = ref(true);
  
  const addTask = (task) => {
    tasks.value.push(task);
  };
  
  const deleteTask = (id) => {
    axios.delete(`http://localhost:3000/tasks/${id}`)
      .then(() => {
        tasks.value = tasks.value.filter(task => task.id !== id);
      })
      .catch(error => {
        console.error('Error deleting task:', error);
      });
  };
  
  const updateTask = (updatedTask) => {
    const index = tasks.value.findIndex(task => task.id === updatedTask.id);
    if (index !== -1) {
      tasks.value[index] = updatedTask;
    }
  };
  
  const deleteCompletedTasks = () => {
    tasks.value = tasks.value.filter(task => !task.completed);
  };
  
  const deleteAllTasks = () => {
    tasks.value = [];
  };
  
  onMounted(async () => {
    loading.value = true;
    try {
      const response = await axios.get('http://localhost:3000/tasks');
      tasks.value = response.data;
    } catch (error) {
      console.error('Error fetching tasks:', error);
    } finally {
      loading.value = false;
    }
  });
  </script>
  
  <style scoped>
    
    body {
    font-family: Arial, sans-serif;
    background-color: #f8f9fa;
    margin: 0;
    padding: 20px;
    }

    .task-list {
    max-width: 600px;
    margin: 0 auto;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    padding: 20px;
    }

    .task-container {
    margin-top: 20px;
    }

    .task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid #ddd;
    }

    .task-item:last-child {
    border-bottom: none;
    }

    .add-task {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
    }

    .add-task input {
    padding: 10px;
    border: 1px solid #ced4da;
    border-radius: 5px;
    margin-bottom: 10px;
    }

    .add-task button {
    padding: 10px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    }

    .add-task button:hover {
    background-color: #0056b3;
    }

    .error-message {
    color: red;
    font-size: 12px;
    }

    .loading {
    text-align: center;
    font-size: 16px;
    color: #6c757d;
    }

    button {
    margin-top: 10px;
    cursor: pointer;
    padding: 10px;
    border: none;
    border-radius: 5px;
    background-color: #dc3545;
    color: white;
    }

    button:hover {
    background-color: #c82333;
    }
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
  </style>