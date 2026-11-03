<script setup>
import { ref, computed } from 'vue';

const emit = defineEmits(['task-completed-final']);

const task = ref(null);
const feedbackMessage = ref('');

const carregarTarefa = () => {
  const saved = localStorage.getItem('habit8_current_task');
  if (saved) {
    task.value = JSON.parse(saved);
  }
};
carregarTarefa();

const salvarNoStorage = () => {
  localStorage.setItem('habit8_current_task', JSON.stringify(task.value));
};

const canCompleteTask = computed(() => {
  if (!task.value) return false;
  const c1Done = task.value.condition1Done;
  const c2Done = task.value.condition2 ? task.value.condition2Done : true;
  return c1Done && c2Done;
});

const currentRewardTarget = computed(() => {
  if (!task.value) return 0;
  return task.value.progress < task.value.intermediateTarget 
    ? task.value.intermediateTarget 
    : task.value.target;
});

const currentRewardText = computed(() => {
  if (!task.value) return '';
  return task.value.progress < task.value.intermediateTarget 
    ? task.value.rewardIntermediate 
    : task.value.rewardFinal;
});

const toggleCondition = () => {
  salvarNoStorage();

  const c1 = task.value.condition1Done;
  const c2 = task.value.condition2 ? task.value.condition2Done : true;
  const hasSecondCondition = !!task.value.condition2;

  // Se tudo foi concluído
  if (c1 && c2) {
    feedbackMessage.value = "Sensacional! Todas as condições foram cumpridas. O botão da tarefa principal foi liberado!";
    return;
  }

  // Se tem duas condições e apenas uma foi marcada
  if (hasSecondCondition) {
    if (c1 && !c2) {
      feedbackMessage.value = "Condição 1 marcada! Falta completar a Condição 2 para liberar sua tarefa.";
      return;
    }
    if (!c1 && c2) {
      feedbackMessage.value = "Condição 2 marcada! Falta completar a Condição 1 para liberar sua tarefa.";
      return;
    }
  } else {
    // Se tem apenas uma condição e ela foi marcada
    if (c1) {
      feedbackMessage.value = "Sensacional! Condição cumprida. O botão de concluir a tarefa está liberado!";
      return;
    }
  }

  // Se desmarcou tudo
  feedbackMessage.value = "";
};

const concluirCiclo = () => {
  if (!canCompleteTask.value) return;

  task.value.progress += 1;
  task.value.condition1Done = false;
  task.value.condition2Done = false;
  salvarNoStorage();

  if (task.value.progress >= task.value.target) {
    emit('task-completed-final');
    return;
  }

  if (task.value.progress === task.value.intermediateTarget) {
    alert(`🎉 PARABÉNS! Você atingiu a metade do caminho (${task.value.progress}/${task.value.target})! Hora de aproveitar sua recompensa intermediária: ${task.value.rewardIntermediate}!`);
  } else {
    const faltam = currentRewardTarget.value - task.value.progress;
    alert(`Incrível! Ciclo ${task.value.progress}/${task.value.target} concluído! Faltam apenas ${faltam} repetições para a sua próxima recompensa! 🚀`);
  }

  feedbackMessage.value = "";
};
</script>

<template>
  <div class="active-task-container" v-if="task">
    <header class="app-header">
      <h1>🚀 Habit8</h1>
      <span class="badge-frequency">
        {{ task.frequency === 'daily' ? '📅 HÁBITO DIÁRIO' : '📆 HÁBITO SEMANAL' }}
      </span>
    </header>

    <section class="reward-banner">
      <div class="reward-icon">🎁</div>
      <div class="reward-info">
        <small>PRÓXIMA RECOMPENSA (em {{ currentRewardTarget }}x):</small>
        <strong>{{ currentRewardText }}</strong>
      </div>
    </section>

    <main class="card-task">
      <h2 class="task-title">{{ task.name }}</h2>

      <div v-if="feedbackMessage" class="feedback-toast">
        ✨ {{ feedbackMessage }}
      </div>

      <section class="step-section">
        <h3>PASSO 1: CUMPRA AS CONDIÇÕES</h3>
        
        <label class="checkbox-card" :class="{ checked: task.condition1Done }">
          <input 
            type="checkbox" 
            v-model="task.condition1Done" 
            @change="toggleCondition"
          />
          <span class="checkmark"></span>
          <span class="condition-text">1. {{ task.condition1 }}</span>
        </label>

        <label 
          v-if="task.condition2" 
          class="checkbox-card" 
          :class="{ checked: task.condition2Done }"
        >
          <input 
            type="checkbox" 
            v-model="task.condition2Done" 
            @change="toggleCondition"
          />
          <span class="checkmark"></span>
          <span class="condition-text">2. {{ task.condition2 }}</span>
        </label>
      </section>

      <section class="step-section">
        <h3>PASSO 2: MARQUE A TAREFA</h3>
        
        <button 
          class="btn-complete-task" 
          :disabled="!canCompleteTask"
          :class="{ unlocked: canCompleteTask }"
          @click="concluirCiclo"
        >
          <span v-if="!canCompleteTask">🔒 CONCLUIR TAREFA</span>
          <span v-else>✅ CONCLUIR TAREFA (LIBERADO!)</span>
        </button>
        <p v-if="!canCompleteTask" class="lock-hint">
          Cumpra a(s) condição(ões) acima para liberar este botão.
        </p>
      </section>

      <section class="progress-section">
        <div class="progress-header">
          <span>PROGRESSO NO CICLO</span>
          <strong>{{ task.progress }} / {{ task.target }} Concluídos</strong>
        </div>
        
        <div class="dots-container">
          <span 
            v-for="index in task.target" 
            :key="index" 
            class="dot"
          >
            {{ index <= task.progress ? '🟢' : '⚪' }}
          </span>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
.active-task-container {
  width: 100%;
  max-width: 480px;
  margin: 0 auto;
  padding: 16px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  color: #333;
  box-sizing: border-box;
}

.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.app-header h1 {
  font-size: 22px;
  color: #2c3e50;
  margin: 0;
}

.badge-frequency {
  background: #e8f4fd;
  color: #2980b9;
  font-size: 11px;
  font-weight: bold;
  padding: 6px 10px;
  border-radius: 20px;
}

.reward-banner {
  background: linear-gradient(135deg, #fff3e0, #ffe0b2);
  border: 1px solid #ffe0b2;
  border-radius: 12px;
  padding: 12px 16px;
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.reward-icon {
  font-size: 24px;
}

.reward-info small {
  display: block;
  font-size: 10px;
  color: #e65100;
  font-weight: bold;
  letter-spacing: 0.5px;
}

.reward-info strong {
  font-size: 14px;
  color: #bf360c;
}

.card-task {
  background: #ffffff;
  padding: 20px;
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
  border: 1px solid #eaeaea;
}

.task-title {
  font-size: 20px;
  color: #2c3e50;
  margin-top: 0;
  margin-bottom: 16px;
  text-align: center;
}

.feedback-toast {
  background: #eafaf1;
  border-left: 4px solid #27ae60;
  color: #1e8449;
  padding: 10px;
  font-size: 13px;
  border-radius: 6px;
  margin-bottom: 16px;
  animation: fadeIn 0.3s ease;
}

.step-section {
  margin-bottom: 20px;
}

.step-section h3 {
  font-size: 12px;
  color: #7f8c8d;
  letter-spacing: 0.5px;
  margin-bottom: 10px;
}

.checkbox-card {
  display: flex;
  align-items: center;
  background: #f9f9f9;
  border: 1px solid #eee;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 8px;
  cursor: pointer;
  transition: all 0.2s;
}

.checkbox-card.checked {
  background: #eafaf1;
  border-color: #a3e4d7;
}

.checkbox-card input {
  display: none;
}

.checkmark {
  width: 20px;
  height: 20px;
  border: 2px solid #ccc;
  border-radius: 4px;
  margin-right: 12px;
  display: inline-block;
  position: relative;
  background: #fff;
}

.checkbox-card.checked .checkmark {
  background: #27ae60;
  border-color: #27ae60;
}

.checkbox-card.checked .checkmark::after {
  content: "✓";
  color: white;
  position: absolute;
  top: -2px;
  left: 4px;
  font-size: 14px;
}

.condition-text {
  font-size: 14px;
  color: #333;
}

.btn-complete-task {
  width: 100%;
  padding: 14px;
  border: none;
  border-radius: 8px;
  background: #bdc3c7;
  color: #fff;
  font-size: 15px;
  font-weight: bold;
  cursor: not-allowed;
  transition: all 0.3s;
}

.btn-complete-task.unlocked {
  background: #27ae60;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(39, 174, 96, 0.3);
}

.btn-complete-task.unlocked:active {
  transform: scale(0.98);
}

.lock-hint {
  font-size: 11px;
  color: #95a5a6;
  text-align: center;
  margin-top: 6px;
}

.progress-section {
  border-top: 1px solid #eee;
  padding-top: 16px;
  margin-top: 16px;
}

.progress-header {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #7f8c8d;
  margin-bottom: 8px;
}

.dots-container {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  justify-content: center;
}

.dot {
  font-size: 16px;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-4px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>