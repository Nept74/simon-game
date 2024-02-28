<template>
  <div id="app">
    <game-board ref="gameBoard" @button-clicked="handleButtonClick" :activeButton="activeButton"></game-board>
    <div v-if="gameOver" class="game-over">Game Over</div>
    <div>Round: {{ round }}</div>
    <button @click="startGame">Start Game</button>
    <select v-model="difficulty">
      <option value="easy">Easy</option>
      <option value="normal">Normal</option>
      <option value="hard">Hard</option>
    </select>
  </div>
</template>

<script>
import GameBoard from './components/GameBoard.vue'; // Убедитесь, что путь к компоненту корректный

export default {
  name: 'App',
  components: {
    GameBoard
  },
  data() {
    return {
      sequence: [],
      userSequence: [],
      activeButton: null,
      difficulty: 'normal',
      round: 0,
      gameOver: false, // Состояние для отображения сообщения о проигрыше
    };
  },
  methods: {
    startGame() {
      this.sequence = [];
      this.userSequence = [];
      this.gameOver = false; // Скрываем сообщение о проигрыше при новой игре
      this.round = 0; // Сброс счетчика раундов
      this.addSequence();
    },
    addSequence() {
      this.sequence.push(Math.floor(Math.random() * 4));
      this.round++; // Увеличиваем счетчик раундов
      this.showSequence();
    },
    showSequence() {
      let index = 0;
      const interval = setInterval(() => {
        this.activateButton(this.sequence[index]);
        index++;
        if (index >= this.sequence.length) {
          clearInterval(interval);
          // Добавляем задержку перед сбросом activeButton для последней кнопки
          setTimeout(() => {
            this.activeButton = null; // Отключить подсветку
          }, this.getIntervalTime());
        }
      }, this.getIntervalTime());
    },
    handleButtonClick(id) {
      if (this.sequence.length === 0 || this.gameOver) return;

      this.activateButton(id); // Активируйте мигание непосредственно здесь

      this.userSequence.push(id);
      const lastIndex = this.userSequence.length - 1;

      if (this.userSequence[lastIndex] !== this.sequence[lastIndex]) {
        this.gameOver = true; // Убедитесь, что это условие выполняется при ошибке
        return;
      }

      if (this.userSequence.length === this.sequence.length) {
        this.userSequence = [];
        setTimeout(() => this.addSequence(), this.getIntervalTime() / 2); // Добавьте небольшую задержку перед добавлением новой последовательности
      }
    },
    activateButton(id) {
      this.activeButton = id;
      this.$refs.gameBoard.playSound(id); // Воспроизведение звука для активной кнопки
      setTimeout(() => {
        this.activeButton = null;
      }, this.getIntervalTime() / 2);
    },
    getIntervalTime() {
      const times = { easy: 1500, normal: 1000, hard: 400 };
      return times[this.difficulty];
    }
  }
};
</script>

<style>
#app {
  text-align: center;
}
</style>

