<template>
  <div class="game-container">
    <div class="grid">
      <div v-for="(cell, index) in grid"
        :key="index" :class="['cell', cell.class]" @click="cellClick(index)">
        {{ cell.value }}
      </div>
    </div>
    <button @click="startGame" :disabled="isGameRunning">{{ startButtonText }}</button>
    <p>{{ message }}</p>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';

export default {
  name: 'MemoryBlockGame',
  setup() {
    const grid = ref([]);
    const sequence = ref([]);
    const playerSequence = ref([]);
    const canClick = ref(false);
    const isGameRunning = ref(false);
    const message = ref('');
    const startButtonText = ref('開始遊戲');

    const gridSize = 25;
    const sequenceLength = 7;

    const showSequence = () => {
      const showNextInSequence = (index) => {
        if (index >= sequence.value.length) {
          canClick.value = true;
          message.value = '請按照順序點擊方塊';
          return;
        }

        const cellIndex = sequence.value[index];
        grid.value[cellIndex].class = 'active';
        grid.value[cellIndex].visible = true;

        setTimeout(() => {
          grid.value[cellIndex].class = '';
          grid.value[cellIndex].visible = false;

          setTimeout(() => {
            showNextInSequence(index + 1);
          }, 500);
        }, 500);
      };

      showNextInSequence(0);
    };

    const startGame = () => {
      if (isGameRunning.value) return;
      isGameRunning.value = true;
      sequence.value = [];
      playerSequence.value = [];
      canClick.value = false;
      message.value = '';
      startButtonText.value = '開始遊戲';

      // 清除所有方塊的數字和類別
      grid.value = Array(gridSize).fill().map(() => reactive({ value: '', class: '', visible: false }));

      // 生成隨機序列
      while (sequence.value.length < sequenceLength) {
        const randomIndex = Math.floor(Math.random() * gridSize);
        if (!sequence.value.includes(randomIndex)) {
          sequence.value.push(randomIndex);
        }
      }

      showSequence();
    };

    const showAllSequence = () => {
      sequence.value.forEach((index, i) => {
        grid.value[index].value = i + 1;
        grid.value[index].class = 'active';
        grid.value[index].visible = true;
      });
    };

    const cellClick = (index) => {
      if (!canClick.value) return;

      playerSequence.value.push(index);

      if (index === sequence.value[playerSequence.value.length - 1]) {
        grid.value[index].class = 'correct';
        grid.value[index].value = playerSequence.value.length;
        grid.value[index].visible = true;

        if (playerSequence.value.length === sequence.value.length) {
          canClick.value = false;
          isGameRunning.value = false;
          message.value = '恭喜你，記憶正確！';
          startButtonText.value = '再玩一次';
        }
      } else {
        grid.value[index].class = 'wrong';
        canClick.value = false;
        isGameRunning.value = false;
        message.value = '很抱歉，記憶錯誤。請查看正確順序。';
        startButtonText.value = '重新開始';
        showAllSequence();
      }
    };

    onMounted(() => {
      grid.value = Array(gridSize).fill().map(() => reactive({ value: '', class: '', visible: false }));
    });

    return {
      grid,
      message,
      startButtonText,
      isGameRunning,
      startGame,
      cellClick,
    };
  },
};
</script>

<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(5, 60px);
  grid-gap: 5px;
  margin-bottom: 20px; /* Adds some space between the grid and the button */
}
.cell {
  width: 60px;
  height: 60px;
  background-color: #ddd;
  border: 1px solid #999;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  font-weight: bold;
}
.cell.active {
  background-color: #4CAF50;
}
.cell.correct {
  background-color: #2196F3;
}
.cell.wrong {
  background-color: #f44336;
}
button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
