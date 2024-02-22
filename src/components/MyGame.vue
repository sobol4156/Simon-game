<template>
  <div class="wrapper">
    <h1>Simon Says</h1>
    <h3></h3>
    <h3></h3>
    <div class="game-board">
      <div class="simon">
        <ul>
          <li
            v-for="(color, index) in colors"
            :key="index"
            @click="playing ? null : handleButtonClick(color)"
            :class="{
              tile: true,
              [color]: true,
              lit: litColor === color,
              pointer: !playing,
            }"
            :value="color"
          ></li>
        </ul>
      </div>
    </div>
    <div class="game-info">
      <h2 v-if="!this.gameOver">
        Round: <span>{{ score }}</span>
      </h2>
      <h3 class="game-info" v-else-if="this.gameOver">
        Вы смогли продержаться <span>{{ score }}</span> раундов!
      </h3>
      <button data-action="start" @click="startSimon">{{ buttonStart }}</button>
    </div>
    <div class="game-options">
      <h2>Game Options:</h2>
      <input
        type="radio"
        name="mode"
        value="easy"
        v-model="options"
        checked
      />Easy<br />
      <input
        type="radio"
        name="mode"
        value="middle"
        v-model="options"
      />Middle<br />
      <input
        type="radio"
        name="mode"
        value="hard"
        v-model="options"
      />Hard<br />
    </div>
  </div>
</template>
  
  <script>
export default {
  data() {
    return {
      score: 0,
      options: "easy",
      buttonStart: "Start",
      colors: ["red", "green", "blue", "yellow"],
      sequence: [],
      userSequence: [],
      indexUser: 0,
      playing: false,
      litColor: null,
      gameOver: false,
      sounds: ["red", "green", "blue", "yellow"],
    };
  },
  methods: {
    startSimon() {
      if (!this.playing) {
        this.playing = true;
        this.buttonStart = "Stop";
        this.indexUser = 0;
        this.gameOver = false;
        this.startGame();
        
      } else {
        this.resetGame();
      }
    },
    getRandomColor() {
      const randomIndex = Math.floor(Math.random() * this.colors.length);
      return this.colors[randomIndex];
    },
    startGame() {
      this.sequence = [];
      this.playSequence();
      
    },
    playSequence() {
      setTimeout(() => {
        this.userSequence = [];
        const delay =
          this.options === "easy"
            ? 1500
            : this.options === "middle"
            ? 1000
            : 400;
        this.sequence.push(this.getRandomColor());

        let index = 0;

        const playNextColor = () => {
          if (index < this.sequence.length) {
            const color = this.sequence[index];
            setTimeout(() => {
              this.playColorSound(color);
              this.litColor = color;
              setTimeout(() => {
                this.litColor = null;
                playNextColor();
              }, delay - delay / 2);
            }, delay);
            index++;
          } else {
            setTimeout(() => {
              this.playing = false;
            }, delay);
          }
        };

        playNextColor();
      }, 1000);
    },

    handleButtonClick(value) {
      const button = document.querySelector(`.${value}`);
      button.classList.add("lit");
      setTimeout(() => {
        button.classList.remove("lit");
      }, 300);
      
      this.playColorSound(value);
      this.userSequence.push(value);

      if (this.indexUser < this.userSequence.length) {
        this.indexUser++;

        if (this.arraysEqual(this.sequence, this.userSequence)) {
          if (this.userSequence.length === this.sequence.length) {
            this.nextRound();
          }
        } else {
          this.gameOver = true;
          this.buttonStart = "Restart";
          this.sequence = [];
          this.userSequence = [];
          this.playing = false;
        }
      }
    },
    playColorSound(color) {
      const audioElement = new Audio(
        `/sounds/${this.colors.indexOf(color) + 1}.mp3`
      );
      audioElement.play();
    },
    arraysEqual(arr1, arr2) {
      if (arr1.length !== arr2.length) return false;
      for (let i = 0; i < arr1.length; i++) {
        if (arr1[i] !== arr2[i]) return false;
      }
      return true;
    },
    nextRound() {
      this.score++;
      this.playSequence();
    },
    resetGame() {
      this.playing = false;
      this.buttonStart = "Start";
      this.sequence = [];
      this.userSequence = [];
      this.gameOver = false;
    },
  },
};
</script>

<style lang="sass" scoped>
h1
    margin: 1em 0 2em
    text-align: center

ul
    list-style: none

ul, li
    padding: 0
    margin: 0

p[data-action="lose"]
    display: none

.active
    opacity: 1 !important

.clearfix
    width: 100%
    clear: both

.wrapper
    width: 540px
    margin: 0 auto

.container
    width: 305px

.simon
    background: #fff
    position: relative
    float: left
    margin-right: 3em
    width: 302px
    height: 295px
    -webkit-border-radius: 150px 150px 150px 150px
    border-radius: 150px 150px 150px 150px
    -moz-box-shadow: 2px 1px 12px #aaa
    -webkit-box-shadow: 2px 1px 12px #aaa
    -o-box-shadow: 2px 1px 12px #aaa
    box-shadow: 2px 1px 12px #aaa

.tile
    opacity: 0.4
    -webkit-transition: opacity 250ms ease
    -moz-transition: opacity 250ms ease
    -ms-transition: opacity 250ms ease
    -o-transition: opacity 250ms ease
    transition: opacity 250ms ease

.tile.pointer
    cursor: pointer

.tile.lit
    opacity: 1

.red,
.blue,
.yellow,
.green
    height: 290px
    -webkit-border-radius: 150px 150px 150px 150px
    border-radius: 150px 150px 150px 150px
    position: absolute
    text-indent: 10000px

.red:hover,
.blue:hover,
.yellow:hover,
.green:hover
    border: 2px solid black

.red
    background: #ff5643
    clip: rect(0px, 300px, 150px, 150px)
    width: 296px

.blue
    background: dodgerblue
    clip: rect(0px, 150px, 150px, 0px)
    width: 300px

.yellow
    background: #feef33
    clip: rect(150px, 150px, 300px, 0px)
    width: 300px

.green
    background: #bede15
    clip: rect(150px, 300px, 300px, 150px)
    width: 296px

.game-info
    margin-top: 90px

button
    cursor: pointer

.game-info button
    width: 5em
    box-sizing: border-box
    font-size: 1.4em
    -webkit-border-radius: 10px 10px 10px 10px
    border-radius: 10px 10px 10px 10px
    background: #6dabe8
    border: none
    padding: 0.3em 0.6em

    &:hover
        background: #78bcff

.game-options h2
    margin-top: 30px
    margin-bottom: 0

.game-options input[type="radio"]
    margin-right: 10px

.hoverable:hover
    cursor: pointer
</style>