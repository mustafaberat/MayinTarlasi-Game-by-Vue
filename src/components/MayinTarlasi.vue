<template>
  <div class="container">
    <!-- TABLE -->
    <table cellspacing="0">
      <tr v-for="(row, rindex) in board" :key="rindex.id">
        <td
          @click="checkMine(rindex, cindex)"
          v-for="(column, cindex) in board"
          :key="column.id"
        >
          <transition name="fade">
            <span v-if="show" :key="board[rindex][cindex].time">{{
              board[rindex][cindex]
            }}</span>
          </transition>
        </td>
      </tr>
    </table>

    <!-- OTHERS -->
    <section>
      <p v-if="language === ''" >
        Choose a language / Dil seçiniz
      </p>
      <p v-if="!gameOver && language === 'TR'">
        Mayınlara basmadan kareleri açınız
      </p>
      <p v-else-if="!gameOver && language === 'ENG'">
        Open squares without stepping on mines
      </p>
      <p v-if="gameOver && language === 'ENG'">Restart with</p>
      <p v-else-if="gameOver && language === 'TR'">Tekrar Başla</p>
      <div class="buttons" v-if="gameOver">
        <button v-if="language === 'TR'" @click="restart('easy')">Kolay</button>
        <button v-else-if="language === 'ENG'" @click="restart('easy')">
          Easy
        </button>
        <button v-if="language === 'TR'" @click="restart('normal')">
          Normal
        </button>
        <button v-else-if="language === 'ENG'" @click="restart('normal')">
          Normal
        </button>
        <button v-if="language === 'TR'" @click="restart('hard')">Zor</button>
        <button v-else-if="language === 'ENG'" @click="restart('hard')">
          Hard
        </button>
      </div>
    </section>
    <vue-dropdown
      :config="config"
      @setSelectedOption="setNewSelectedOption($event)"
      v-if="!chosenLanguage"
    ></vue-dropdown>
  </div>
</template>

<script>
// import vueDropdown from 'vue-dynamic-dropdown'
import vueDropdown from './vue-dropdown/vue-dropdown'

export default {
  name: "MayinTarlasi",
  components: {
    vueDropdown,
  },
  data() {
    return {
      show: true,
      chosenLanguage: false,
      gameOver: false,
      diff: "hard",
      language: "",
      clickable: false,
      minedBoard: [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ],
      board: [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ],
      config: {
        options: [
          {
            value: "ENG",
          },
          {
            value: "TR",
          },
        ],
        placeholder: "Language",
        backgroundColor: "#cde4f5",
        textColor: "black",
        borderRadius: "1.5em",
        border: "1px solid gray",
        width: 180,
      },
    };
  },
  created: function () {
    if(this.chosenLanguage){
      this.getInitialMines();
      this.hideMines();
    } 
  },

  methods: {
    restart(difficulty) {
      this.diff = difficulty;
      this.makeBoardsEmpty();
      this.gameOver = false;
      this.getInitialMines();
      this.hideMines();
    },

    makeBoardsEmpty() {
      this.board = [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ];
      this.minedBoard = [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ];
    },

    makeOnlyBoardEmpty() {
      this.board = [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ];
    },

    showMinedBoard() {
      this.board = this.minedBoard;
    },

    checkMine(x, y) {
      try {
        if (this.clickable && !this.gameOver) {
          if (this.minedBoard[x][y] === "x") {
            this.showMinedBoard();
            this.gameOver = true;
            this.clickable = false;
          } else if (this.minedBoard[x][y] === "") {
            this.board[x][y] = "✓";
            this.minedBoard[x][y] = "✓";
            this.$forceUpdate();
          }
        }
      } catch (error) {
        console.error(error + "in trycatch");
      }
    },

    getInitialMines() {
      if (!this.gameOver) {
        let numberOfRandomMine;
        if (this.diff === "hard") {
          numberOfRandomMine = this.board.length * 3; //if 25 => 15
        } else if (this.diff === "normal") {
          numberOfRandomMine = this.board.length * 2; //if 25 => 10
        } else if (this.diff === "easy") {
          numberOfRandomMine = this.board.length; //if 25 => 5
        }
        while (numberOfRandomMine > 0) {
          let randNoX = Math.round(Math.random() * (this.board.length - 1));
          let randNoY = Math.round(Math.random() * (this.board.length - 1));
          if (this.board[randNoX][randNoY] === "") {
            this.board[randNoX][randNoY] = "x";
            this.minedBoard[randNoX][randNoY] = "x";
            numberOfRandomMine -= 1;
          }
        }
      } else {
        console.error("GetInitialMines tells wrong gameover value");
      }
    },

    hideMines() {
      try {
        this.show = true;
        if (this.diff === "easy") {
          setTimeout(
            function () {
              this.makeOnlyBoardEmpty();
              this.clickable = true;
            }.bind(this),
            6000,
          );
        } else if (this.diff === "normal") {
          setTimeout(
            function () {
              this.makeOnlyBoardEmpty();
              this.clickable = true;
            }.bind(this),
            4000,
          );
        } else if (this.diff === "hard") {
          setTimeout(
            function () {
              this.makeOnlyBoardEmpty();
              this.clickable = true;
            }.bind(this),
            2000,
          );
        } else {
          console.error("Hide Mines function error because of ifs");
        }
      } catch (error) {
        console.error(error + "In catch");
      }
    },

    setNewSelectedOption(selectedOption) {
      this.config.placeholder = selectedOption.value;
      this.language = selectedOption.value;
      this.clickable = false;
      this.chosenLanguage = true;
      this.restart(this.diff);
    },
  },
};
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
