<script setup>
import { ref, computed } from 'vue';

const emit = defineEmits(['reset-to-create']);

const task = ref(null);

const carregarTarefa = () => {
  const saved = localStorage.getItem('habit8_current_task');
  if (saved) {
    task.value = JSON.parse(saved);
  }
};
carregarTarefa();

const dataFormatada = computed(() => {
  const hoje = new Date();
  return hoje.toLocaleDateString('pt-BR');
});

const compartilharOuBaixar = () => {
  if (navigator.share) {
    navigator.share({
      title: 'Meu Hábito Concluído!',
      text: `🏆 Concluí o hábito "${task.value.name}" no app Habit8!`,
      url: window.location.href
    }).catch(() => {});
  } else {
    alert('Tire um print da tela para postar no seu Instagram Story! 📸');
  }
};

const novoHabito = () => {
  localStorage.removeItem('habit8_current_task');
  emit('reset-to-create');
};
</script>

<template>
  <div class="certificate-view-container" v-if="task">
    <header class="app-header">
      <h1>🎉 PARABÉNS! 🎉</h1>
      <p>Você transformou essa ação em um hábito de verdade!</p>
    </header>

    <div class="story-card-wrapper">
      <div class="story-card" id="instagram-certificate">
        <div class="badge-trophy">🌟🏆✨</div>
        
        <div class="card-header-text">
          <h2>CERTIFICADO DE VITÓRIA</h2>
          <p class="subtitle">Missão cumprida com foco e determinação!</p>
        </div>

        <div class="task-highlight">
          "{{ task.name }}"
        </div>

        <div class="details-box">
          <div class="detail-item">
            <span>FREQUÊNCIA:</span>
            <strong>{{ task.frequency === 'daily' ? 'Diária (10 Ciclos)' : 'Semanal (6 Ciclos)' }}</strong>
          </div>
          <div class="detail-item">
            <span>RECOMPENSA CONQUISTADA:</span>
            <strong>🎁 {{ task.rewardFinal }}</strong>
          </div>
          <div class="detail-item">
            <span>DATA DA CONQUISTA:</span>
            <strong>{{ dataFormatada }}</strong>
          </div>
        </div>

        <footer class="card-footer">
          <div class="app-brand">🚀 Habit8</div>
          <small>O poder do 1 passo por vez</small>
        </footer>
      </div>
    </div>

    <div class="actions-container">
      <button class="btn-share" @click="compartilharOuBaixar">
        📥 Compartilhar / Salvar Card
      </button>

      <button class="btn-new-habit" @click="novoHabito">
        ➕ Criar Meu Próximo Hábito
      </button>
    </div>
  </div>
</template>

<style scoped>
.certificate-view-container {
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
  margin-bottom: 16px;
}

.app-header h1 {
  font-size: 22px;
  color: #1b4965;
  margin: 0 0 4px 0;
}

.app-header p {
  font-size: 13px;
  color: #666;
  margin: 0;
}

.story-card-wrapper {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

/* Card com tons de Azul Profundo e Dourado Sofisticado */
.story-card {
  width: 100%;
  max-width: 320px;
  aspect-ratio: 9 / 16;
  background: linear-gradient(135deg, #0b1d3a, #1b4965, #2c698d);
  color: #ffffff;
  border-radius: 24px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  text-align: center;
  box-shadow: 0 12px 30px rgba(11, 29, 58, 0.4);
  box-sizing: border-box;
  border: 3px solid #e2b714; /* Borda em tom dourado */
}

.badge-trophy {
  font-size: 32px;
  margin-top: 6px;
  filter: drop-shadow(0 2px 4px rgba(0,0,0,0.3));
}

.card-header-text h2 {
  font-size: 15px;
  letter-spacing: 1.5px;
  color: #f3c623; /* Dourado brilhante */
  margin: 4px 0;
  text-shadow: 0 1px 3px rgba(0,0,0,0.4);
}

.subtitle {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.85);
  margin: 0;
  font-weight: 500;
}

.task-highlight {
  font-size: 19px;
  font-weight: 800;
  color: #0b1d3a;
  background: linear-gradient(135deg, #f3c623, #ffd700); /* Fundo em gradiente dourado */
  padding: 14px 10px;
  border-radius: 12px;
  width: 100%;
  box-sizing: border-box;
  word-break: break-word;
  box-shadow: 0 4px 15px rgba(243, 198, 35, 0.3);
  border: 1px solid #ffe875;
}

.details-box {
  width: 100%;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(4px);
  padding: 12px;
  border-radius: 12px;
  text-align: left;
  border: 1px solid rgba(243, 198, 35, 0.25);
}

.detail-item {
  margin-bottom: 6px;
  font-size: 11px;
}

.detail-item:last-child {
  margin-bottom: 0;
}

.detail-item span {
  display: block;
  color: #f3c623; /* Títulos dos detalhes em dourado */
  font-size: 9px;
  font-weight: bold;
  letter-spacing: 0.5px;
}

.detail-item strong {
  color: #ffffff;
  font-size: 12px;
}

.card-footer {
  border-top: 1px solid rgba(243, 198, 35, 0.25);
  width: 100%;
  padding-top: 8px;
}

.app-brand {
  font-weight: 800;
  font-size: 15px;
  color: #f3c623;
  letter-spacing: 0.5px;
}

.card-footer small {
  font-size: 9px;
  color: rgba(255, 255, 255, 0.8);
}

.actions-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.btn-share {
  width: 100%;
  background: #1b4965;
  color: #f3c623;
  border: 2px solid #f3c623;
  padding: 14px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(27, 73, 101, 0.25);
  transition: transform 0.1s;
}

.btn-share:active {
  transform: scale(0.98);
}

.btn-new-habit {
  width: 100%;
  background: #f3c623;
  color: #0b1d3a;
  border: none;
  padding: 14px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(243, 198, 35, 0.3);
  transition: transform 0.1s;
}

.btn-new-habit:active {
  transform: scale(0.98);
}
</style>