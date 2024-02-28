<template>
  <div class="game-board">
    <div v-for="button in buttons" :key="button.id" class="game-button"
      :class="{ 'active': button.id === activeButton, 'button-0': button.id === 0, 'button-1': button.id === 1, 'button-2': button.id === 2, 'button-3': button.id === 3 }"
      @click="buttonClicked(button.id)"></div>
  </div>
</template>

<script>
export default {
  name: 'GameBoard',
  props: ['activeButton'],
  data() {
    return {
      buttons: [
        { id: 0, sound: 'sound0.mp3' },
        { id: 1, sound: 'sound1.mp3' },
        { id: 2, sound: 'sound2.mp3' },
        { id: 3, sound: 'sound3.mp3' }
      ],
      audioCache: {}
    };
  },
  created() {
    this.buttons.forEach(button => {
      const soundPath = require(`../assets/sounds/${button.sound}`);
      this.audioCache[button.id] = new Audio(soundPath);
    });
  },
  methods: {
    buttonClicked(id) {
      this.$emit('button-clicked', id);
      this.playSound(id);
    },
    playSound(id) {
      if (this.audioCache[id]) {
        this.audioCache[id].currentTime = 0;
        this.audioCache[id].play();
      }
    }
  }
};
</script>

<style>
.game-board {
  display: grid;
  grid-template-columns: repeat(2, 1fr); /* Создаем две колонки с одинаковой шириной */
  grid-gap: 10px; /* Расстояние между элементами сетки */
  width: 210px; /* Ширина доски, может потребоваться корректировка */
  margin: auto;
}

.game-button {
  width: 50%;
  height: 100px;
  width: 100px;
  border: 1px solid black;
  box-sizing: border-box;
  opacity: 0.5;
  cursor: pointer;
  user-select: none;
}

.button-0 {
  background-color: red;
}

.button-1 {
  background-color: green;
}

.button-2 {
  background-color: blue;
}

.button-3 {
  background-color: yellow;
}

.active {
  animation: blink-animation 1s steps(2, start) infinite;
}

@keyframes blink-animation {
  to {
    opacity: 1;
  }
}
</style>
