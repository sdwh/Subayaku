<template>
  <div class="game-container">
    <div id="size-selector">
      <label for="game-size">選擇遊戲大小：</label>
      <select v-model="gameSize" id="game-size">
        <option value="4">4x4</option>
        <option value="6">6x6</option>
        <option value="8">8x8</option>
      </select>
      <div style="margin-top: 1rem;">
        <button @click="startGame" style="margin-right: 2rem;">開始新遊戲</button>
        <span id="moves">翻開次數: {{ moves }}</span>
      </div>
    </div>
    <div class="game-board" :style="gameBoardStyle">
      <div
        v-for="(card, index) in cards"
        :key="index"
        class="card"
        :class="{ flipped: card.flipped }"
        :style="cardStyle"
        @click="flipCard(index)"
      >
        {{ card.flipped ? card.emoji : '' }}
      </div>
    </div>
  </div>
</template>

<script>
const emojis = [
  '🚗', '🚕', '🎮', '🚌', '🚎', '🏎️', '🚓', '🚑', '🚒', '🚐', '🥽', '🚚', '🚛', '🚜', '🛵', '🏍️',
  '🍎', '🍐', '🍊', '🍋', '🍌', '🍉', '🍇', '🍓', '🎨', '🍈', '🍒', '🍑', '🥭', '🍍', '🥥', '🥝',
  '🍕', '🍔', '🌭', '🍟', '🌮', '🍣', '🍱', '🥟', '🍜', '🍝', '🍲', '🥘', '🧆', '🥗', '🥪', '🥙',
  '🐶', '🐱', '🐭', '🐰', '🦊', '🐻', '🐼', '🐨', '🐯', '🦁', '🐮', '🐷', '🐸', '🐵', '🐔', '🐧',
  '⚽', '🏀', '🏈', '⚾', '🎾', '🏐', '🏉', '🥏', '🎱', '🪀', '🏓', '🏸', '🏒', '🏑', '🥍', '🏏',
  '🌞', '🌙', '⭐', '🌈', '☁️', '❄️', '🌊', '🌴', '🌵', '🌷', '🌹', '💎', '🌸', '🌼', '🌻', '🍄',
];

export default {
  data() {
    return {
      gameSize: 4,
      cards: [],
      flippedCards: [],
      moves: 0,
      matchedPairs: 0,
    };
  },
  computed: {
    gameBoardStyle() {
      return {
        display: 'grid',
        gap: '10px',
        gridTemplateColumns: `repeat(${this.gameSize}, 1fr)`,
        width: '360px',
        height: '360px',
      };
    },
    cardStyle() {
      const size = `calc((360px - ${(this.gameSize - 1) * 10}px) / ${this.gameSize})`;
      return {
        width: size,
        height: size,
      };
    },
  },
  watch: {
    gameSize: {
      handler(newSize) {
        this.gameSize = parseInt(newSize, 10);
        this.startGame();
      },
      immediate: true,
    },
  },
  methods: {
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i -= 1) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },
    createBoard() {
      this.cards = [];
      this.flippedCards = [];
      this.moves = 0;
      this.matchedPairs = 0;

      const totalPairs = (this.gameSize * this.gameSize) / 2;

      const shuffledEmojis = [...emojis];
      this.shuffleArray(shuffledEmojis);
      const selectedEmojis = shuffledEmojis.slice(0, totalPairs);
      const pairedEmojis = [...selectedEmojis, ...selectedEmojis];
      this.shuffleArray(pairedEmojis);

      this.cards = pairedEmojis.map((emoji) => ({ emoji, flipped: false }));
    },
    flipCard(index) {
      if (this.flippedCards.length < 2 && !this.cards[index].flipped) {
        this.cards[index].flipped = true;
        this.flippedCards.push(index);

        if (this.flippedCards.length === 2) {
          this.moves += 1;
          setTimeout(this.checkMatch, 300);
        }
      }
    },
    checkMatch() {
      const [index1, index2] = this.flippedCards;
      if (this.cards[index1].emoji === this.cards[index2].emoji) {
        this.matchedPairs += 1;
        if (this.matchedPairs === (this.gameSize * this.gameSize) / 2) {
          console.log('Game Over!');
        }
      } else {
        this.cards[index1].flipped = false;
        this.cards[index2].flipped = false;
      }
      this.flippedCards = [];
    },
    startGame() {
      this.gameSize = parseInt(this.gameSize, 10);
      this.createBoard();
    },
  },
  mounted() {
    this.startGame();
  },
};
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  background-color: #f0f0f0;
}

.game-container {
  text-align: center;
}

.game-board {
  margin: 0 auto;
  margin-bottom: 20px;
}

.card {
  background-color: #3498db;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.card.flipped {
  background-color: #2ecc71;
}

#moves {
  font-size: 18px;
  margin-bottom: 10px;
}

#size-selector {
  margin-bottom: 20px;
}
</style>
