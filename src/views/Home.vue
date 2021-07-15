<template>
  <div class="home">
    <div class="start-btn" v-if="!isGameStart" @click="gameStart()">START</div>

    <div v-if="isGameStart">
      <!-- Debug Mode -->
      <!-- <div class="section-question">{{ questionString }} | {{ answer }}</div> -->
      <div class="section-question">{{ questionString }}</div>
      <div class="section-answer">
        <input
          class="input-answer"
          type="number"
          ref="inputAnswer"
          pattern="\d*"
          v-model.number="userAnswer"
          v-on:keyup.enter="updateQuestion()"
          autofocus
        />
        <span class="info" v-show="compareAnswer">
          <span class="btn" @click="updateQuestion()">Next</span>
        </span>
      </div>
      <div>Completed: {{ completed.length }}</div>

      <div class="completed-list" v-for="q in completed" :key="q">
        {{ q }}
      </div>
    </div>

  </div>
</template>

<script>
function getRandomHex() {
  const randVal = Math.floor(Math.random() * 255) + 1;
  const hex = randVal.toString(16).toUpperCase();
  return {
    quesString: `Hex ${hex} = ?`,
    answer: randVal,
  };
}

function getRandomBinary() {
  const randVal = Math.floor(Math.random() * 256) + 1;
  const binary = randVal.toString(2).padStart(8, '0');
  return {
    quesString: `Bin ${binary.slice(0, 4)} ${binary.slice(4, 8)} = ?`,
    answer: randVal,
  };
}

function getRandomAddition() {
  const randValLeft = Math.floor(Math.random() * 999) + 1;
  const randValRight = Math.floor(Math.random() * 999) + 1;
  const sum = randValLeft + randValRight;
  return {
    quesString: `${randValLeft} + ${randValRight} = ?`,
    answer: sum,
  };
}

function getRandomMultiply() {
  const randValLeft = Math.floor(Math.random() * 25) + 1;
  const randValRight = Math.floor(Math.random() * 25) + 1;
  const product = randValLeft * randValRight;
  return {
    quesString: `${randValLeft} × ${randValRight} = ?`,
    answer: product,
  };
}

function getRandomContinuousAdd() {
  const randVal = () => (Math.floor(Math.random() * 10) + 1) * 5;
  const times = 5;
  const numbers = [];
  let answer = 0;
  for (let index = 0; index < times; index += 1) {
    const genRandVal = randVal();
    numbers.unshift(genRandVal);
    answer += genRandVal;
  }

  return {
    quesString: `${numbers.join(' + ')} = ?`,
    answer,
  };
}

// import HelloWorld from '@/components/HelloWorld.vue';
export default {
  name: 'Home',
  data() {
    return {
      questionString: 'Hex FF = ?',
      answer: 255,
      userAnswer: '',
      completed: [],
      isGameStart: false,
    };
  },
  computed: {
    compareAnswer() {
      return this.answer === this.userAnswer;
    },
  },
  components: {},
  watch: {
    userAnswer() {
      this.updateQuestion();
    },
  },
  methods: {
    gameStart() {
      this.isGameStart = !this.isGameStart;
      this.updateQuestion();
    },
    updateQuestion() {
      const quesBankObject = [
        getRandomHex,
        getRandomBinary,
        getRandomAddition,
        getRandomMultiply,
        getRandomContinuousAdd];

      if (this.answer === this.userAnswer) {
        this.completed.unshift(this.questionString.replace('?', this.answer));
        const quesObject = quesBankObject[Math.floor(Math.random() * quesBankObject.length)]();
        this.answer = quesObject.answer;
        this.questionString = quesObject.quesString;
        this.userAnswer = '';
        this.$refs.inputAnswer.focus();
      }
    },
  },
};
</script>

<style scoped lang="scss">
.section-question {
  background-color: skyblue;
  width: 100%;
  margin: 0 auto;
  padding: 10px 0;
  font-size: 1.5rem;
}

.section-answer {
  margin-top: 15px;
  padding: 3px;
}

.input-answer {
  padding: 5px;
  font-size: 1.25rem;
}

.info {
  margin: 10px;
  font-size: 1.25rem;
}

.btn {
  background-color: lightseagreen;
  color: blanchedalmond;
  padding: 8px;
  border-radius: 5px;

  &:hover {
    cursor: pointer;
    background-color: seagreen;
  }
}

.completed-list {
  width: 80%;
  margin: 0 auto;
  margin-top: 15px;
  line-height: 1rem;
  text-align: left;
}

.start-btn{
  /* creative by https://codepen.io/reulison/pen/WNNVPZq */
  font-family: 'Press Start 2p';
  font-size: 3rem;
  text-align: center;
  display: inline-block;
  margin-top: 100px;
  font-weight: bold;
  padding: 15px 30px ;
  background-color: lightgray;
  text-shadow: -1px -1px black, 1px 1px white;
  color: gray;
  border-radius: 7px;
  box-shadow: 0 .2em gray;
  cursor: pointer;

  &:active{
    box-shadow: none;
    position: relative;
    top: .2em;
  }
}
</style>
