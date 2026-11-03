<script setup>
import { reactive } from 'vue';

const emit = defineEmits(['task-created']);

const task = reactive({
  name: '',
  frequency: 'weekly',
  condition1: '',
  condition2: '',
  rewardIntermediate: '',
  rewardFinal: '',
  progress: 0
});

const salvarTarefa = () => {
  if (!task.condition1.trim()) {
    alert('Você precisa definir pelo menos 1 condição!');
    return;
  }

  const novaTarefaAtiva = {
    ...task,
    target: task.frequency === 'daily' ? 10 : 6,
    intermediateTarget: task.frequency === 'daily' ? 5 : 3,
    condition1Done: false,
    condition2Done: false,
    taskDoneToday: false
  };

  localStorage.setItem('habit8_current_task', JSON.stringify(novaTarefaAtiva));
  emit('task-created');
};
</script>

<template>
  <div class="create-task-container">
    <header class="app-header">
      <h1>Habit8</h1>
      <p>Um passo de cada vez. Crie seu novo hábito!</p>
    </header>

    <main class="card-form">
      <div class="tip-box">
        💡 <strong>Dica:</strong> Mantenha sua meta simples e realista de alcançar!
      </div>

      <form @submit.prevent="salvarTarefa">
        <div class="form-group">
          <label for="taskName">Qual é a sua nova tarefa?</label>
          <input 
            type="text" 
            id="taskName" 
            v-model="task.name" 
            placeholder="Ex: Ler 10 páginas / Limpar o banheiro" 
            required 
          />
        </div>

        <div class="form-group">
          <label>Frequência da Tarefa:</label>
          <div class="frequency-options">
            <label :class="{ active: task.frequency === 'daily' }">
              <input type="radio" value="daily" v-model="task.frequency" />
              <span>Diária <small>(10 repetições)</small></span>
            </label>
            <label :class="{ active: task.frequency === 'weekly' }">
              <input type="radio" value="weekly" v-model="task.frequency" />
              <span>Semanal <small>(6 repetições)</small></span>
            </label>
          </div>
        </div>

        <div class="form-group">
          <label>Condições para Concluir (De 1 até 2 condições):</label>
          <input 
            type="text" 
            v-model="task.condition1" 
            placeholder="Condição 1 (Ex: Acordar mais cedo)" 
            required 
          />
          <input 
            type="text" 
            v-model="task.condition2" 
            placeholder="Condição 2 (Opcional, ex: Estar com o livro na mesa)" 
          />
        </div>

        <div class="form-group rewards-section">
          <label>Suas Recompensas:</label>
          <p class="reward-tip">
            💡 Escolha algo menor para a metade do caminho e algo especial para a conclusão final!
            Seja específico na recompens
          </p>
          
          <input 
            type="text" 
            v-model="task.rewardIntermediate" 
            :placeholder="'Recompensa Intermediária (ex: Tomar um açaí)'" 
            required 
          />
          
          <input 
            type="text" 
            v-model="task.rewardFinal" 
            :placeholder="'Recompensa Final (ex: Comprar um fone de ouvido)'" 
            required 
          />
        </div>

        <button type="submit" class="btn-submit">🚀 Começar Hábito</button>
      </form>
    </main>
  </div>
</template>

<style scoped>
.create-task-container {
  width: 100%;
  max-width: 480px;
  margin: 0 auto;
  padding: 16px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  color: #333;
  box-sizing: border-box;
}

.app-header {
  text-align: center;
  margin-bottom: 20px;
}

.app-header h1 {
  color: #2c3e50;
  font-size: 24px;
  margin-bottom: 4px;
}

.app-header p {
  font-size: 14px;
  color: #666;
}

.card-form {
  background: #ffffff;
  padding: 20px;
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
  border: 1px solid #eaeaea;
}

.tip-box {
  background: #e8f4fd;
  border-left: 4px solid #3498db;
  padding: 12px;
  font-size: 13px;
  border-radius: 6px;
  margin-bottom: 16px;
  color: #2980b9;
  line-height: 1.4;
}

.form-group {
  margin-bottom: 16px;
}

.form-group label {
  display: block;
  font-weight: 600;
  margin-bottom: 6px;
  font-size: 13px;
  color: #444;
}

.form-group input[type="text"] {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 15px;
  margin-bottom: 8px;
  box-sizing: border-box;
  outline: none;
  transition: border-color 0.2s;
}

.form-group input[type="text"]:focus {
  border-color: #27ae60;
}

.frequency-options {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

@media (min-width: 400px) {
  .frequency-options {
    flex-direction: row;
  }
}

.frequency-options label {
  flex: 1;
  border: 1px solid #ddd;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  cursor: pointer;
  background: #fafafa;
  font-weight: normal;
  font-size: 14px;
  transition: all 0.2s;
}

.frequency-options label.active {
  border-color: #27ae60;
  background: #eafaf1;
  color: #27ae60;
  font-weight: bold;
}

.frequency-options input {
  display: none;
}

.rewards-section {
  border-top: 1px solid #f0f0f0;
  padding-top: 16px;
  margin-top: 16px;
}

.reward-tip {
  font-size: 12px;
  color: #666;
  margin-bottom: 10px;
  line-height: 1.3;
}

.btn-submit {
  width: 100%;
  background: #27ae60;
  color: white;
  border: none;
  padding: 14px;
  border-radius: 8px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.2s;
  margin-top: 8px;
  box-shadow: 0 4px 10px rgba(39, 174, 96, 0.2);
}

.btn-submit:active {
  transform: scale(0.98);
}

.btn-submit:hover {
  background: #219653;
}
</style>