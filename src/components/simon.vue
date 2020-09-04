<template>
  <div class="main-page">
    <h1 class="justGame">Simon says</h1>
    <div class="control" v-on:click.prevent="checkResult">
      <div class="first-item items" :class="{ active: currentStyle == 1 }" data-id="1"></div>
      <div class="second-item items" data-id="2" :class="{ active: currentStyle == 2 }"></div>
      <div class="third-item items" data-id="3" :class="{ active: currentStyle == 3 }"></div>
      <div class="fourth-item items" data-id="4" :class="{ active: currentStyle == 4 }"></div>
    </div>
    <div class="info">
      <button v-on:click.prevent="startGame" class="start-button">Начать</button>
      <div class="difficult" v-on:click.prevent="chooseDifficult">
        <h5 v-if="round">Раунд: {{ round }}</h5>
        <h4>Уровень сложности</h4>
        <button data-time="1500">Легкий</button>
        <button data-time="1000">Средний</button>
        <button data-time="400">Сложный</button>
      </div>
      <h2 v-if="end">Игра закончена</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: "simon",
  data() {
    return {
      computerVariant: [],
      userVariant: [],
      counter: 0,
      currentStyle: 0,
      end: false,
      round: 0,
      clickTimes: 0,
      difficultyTime: 1500,
    };
  },

  props: {},
  methods: {
    changeStyle() {
      //меняем стили у активных элементов.
      this.currentStyle = event.target.dataset.id;
      setTimeout(() => {
        this.currentStyle = 0;
      }, 300);
    },
    playAudio(event) {
      //функция проигрывания звуков по нажатию

      var audio = new Audio(
        require(`../assets/audio/${event.target.dataset.id}.mp3`)
      );
      audio.play();
      this.changeStyle();
    },
    getRandomNumber() {
      //функция рандомных чисел
      return Math.round(1 - 0.5 + Math.random() * (4 - 1 + 1));
    },
    clearData() {
      this.end = false;
      this.clickTimes = 0;
      this.userVariant = [];
    },
    startGame() {
      this.clearData();
      let rand = this.getRandomNumber();
      this.computerVariant.push(rand);
      this.round++;
      let timeoutFunc = setInterval(() => {
        //проигрываем все значения из компьютерного варианта
        var audio = new Audio(
          require(`../assets/audio/${this.computerVariant[this.counter]}.mp3`)
        );
        audio.play();
        this.currentStyle = this.computerVariant[this.counter];
        setTimeout(() => {
          this.currentStyle = 0;
        }, 300);
        this.counter++;
        if (this.counter >= this.computerVariant.length) {
          clearInterval(timeoutFunc);
          this.counter = 0;
        }
      }, this.difficultyTime);
    },
    isEqual(arr, subarr) {
      //сравнение массивов
      return subarr.every((v, i) => v == arr[i]);
    },
    checkResult(event) {
      //вводим данные в пользовательский массив и сравниваем значения со значениями
      //рандомного массива. Если ошиблись, то игра заканчивается
      if (!event.target.dataset.id || this.computerVariant.length == 0) return;
      this.userVariant.push(event.target.dataset.id);
      this.playAudio(event);
      this.clickTimes++;
      if (this.isEqual(this.computerVariant, this.userVariant)) {
        if (this.clickTimes == this.computerVariant.length) {
          this.startGame();
        }
        return;
      } else {
        this.endGame();
      }
    },
    endGame() {
      this.end = true;
      this.userVariant = [];
      this.computerVariant = [];
    },
    chooseDifficult(event) {
      if (!event.target.dataset.time) return;
      this.difficultyTime = event.target.dataset.time;
    },
  },
};
</script>

<style scoped lang="scss">
$color-red: rgba(214, 49, 49, 0.8);
* {
  box-sizing: border-box;
}
.main-page {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  h1 {
    font-size: 3em;
    margin: 1em;
  }
}
.justGame {
  width: 100%;
}
.control {
  display: flex;
  width: 350px;
  flex-wrap: wrap;
}
.items {
  width: 150px;
  height: 150px;
  background: black;
}
.first-item {
  opacity: 0.6;
  background: rgba(214, 49, 49, 1);
  border-radius: 100% 0 0 0;
}
.second-item {
  opacity: 0.6;
  background: rgba(40, 97, 184, 1);
  border-radius: 0 100% 0 0;
}
.third-item {
  opacity: 0.6;
  background: rgba(32, 151, 32, 1);
  border-radius: 0 0 0 100%;
}
.fourth-item {
  opacity: 0.6;
  background: rgb(226, 195, 59);
  border-radius: 0 0 100% 0;
}

.active {
  opacity: 1;
  border: 2px solid black;
  transition: all 0.3s ease-in;
}

.start-button {
  width: 100px;
  height: 50px;
}
.difficult {
  button {
    height: 40px;
    &:active {
      background: chocolate;
    }
  }
}
</style>
