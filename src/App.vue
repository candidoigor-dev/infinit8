<script setup>
import { ref, onMounted } from 'vue';
import CreateTask from './views/CreateTask.vue';
import ActiveTask from './views/ActiveTask.vue';
import Certificate from './views/Certificate.vue';

const currentScreen = ref('create');

const checkCurrentTask = () => {
  const saved = localStorage.getItem('habit8_current_task');
  if (!saved) {
    currentScreen.value = 'create';
    return;
  }

  const task = JSON.parse(saved);
  if (task.progress >= task.target) {
    currentScreen.value = 'certificate';
  } else {
    currentScreen.value = 'active';
  }
};

const goToCertificate = () => {
  currentScreen.value = 'certificate';
};

onMounted(() => {
  checkCurrentTask();
});
</script>

<template>
  <div id="app">
    <CreateTask 
      v-if="currentScreen === 'create'" 
      @task-created="checkCurrentTask" 
    />
    
    <ActiveTask 
      v-else-if="currentScreen === 'active'" 
      @task-completed-final="goToCertificate" 
    />
    
    <Certificate 
      v-else-if="currentScreen === 'certificate'" 
      @reset-to-create="checkCurrentTask" 
    />
  </div>
</template>

<style>
body {
  margin: 0;
  background-color: #f4f7f6;
}
</style>