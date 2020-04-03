<template>
  <div class="container">
    <!-- TABLE -->
    <table cellspacing="0">
      <tr v-for="(row, rindex) in board" :key="row.id">
        <td
          @click="checkMine(rindex, cindex)"
          v-for="(column, cindex) in row"
          :key="column.id"
        >
          <transition name="fade">
            <span v-if="show" :key="step">{{ board[rindex][cindex] }}</span>
          </transition>
        </td>
      </tr>
    </table>

    <!-- OTHERS -->
    <section>
      <p v-if="!gameOver">Mayınlara basmadan her kareyi açınız</p>
      <button v-if="gameOver" @click="restart()">Restart</button>
    </section>
  </div>
</template>

<script>
export default {
  name: "MayinTarlasi",
  data() {
    return {
      show: true,
      gameOver: false,
      diff: "hard",
      step: 0,
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
    };
  },
  created: function() {
    this.getInitialMines();
    this.hideMines();
  },

  methods: {
    restart() {
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
      console.log("Board and minedBoard empty anymore");
      console.table(this.board);
      console.table(this.minedBoard);
    },

    makeOnlyBoardEmpty() {
      this.board = [
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
        ["", "", "", "", ""],
      ];
      console.log("Board is empty anymore");
      console.table(this.board);
    },

    showMinedBoard() {
      this.board = this.minedBoard;
    },

    checkMine(x, y) {
      try {
        if (this.clickable && !this.gameOver) {
          if (this.minedBoard[x][y] === "X") {
            this.showMinedBoard();
            this.gameOver = true;
            this.clickable = false;
            this.step += 1;
          } else if (this.minedBoard[x][y] === "") {
            this.board[x][y] = "C";
            this.minedBoard[x][y] = "C";
            this.step += 1;
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
          numberOfRandomMine = this.board.length * 3;
        } else if (this.diff === "normal") {
          numberOfRandomMine = this.board.length * 2;
        } else if (this.diff === "easy") {
          numberOfRandomMine = this.board.length;
        }
        while (numberOfRandomMine > 0) {
          let randNoX = Math.round(Math.random() * (this.board.length - 1));
          let randNoY = Math.round(Math.random() * (this.board.length - 1));
          if (this.board[randNoX][randNoY] === "") {
            this.board[randNoX][randNoY] = "X";
            this.minedBoard[randNoX][randNoY] = "X";
            numberOfRandomMine -= 1;
          }
        }
      } else {
        console.error("GetInitialMines tells wrong gameover value");
      }
      console.log("First Board Photo:");
      console.table(this.board);
    },

    hideMines() {
      try {
        this.show = true;
        if (this.diff === "easy") {
          setTimeout(
            function() {
              this.show = false;
              this.show = true;
              this.makeOnlyBoardEmpty();
              this.clickable = true;
              console.table(this.board);
            }.bind(this),
            6000,
          );
        } else if (this.diff === "normal") {
          setTimeout(
            function() {
              this.show = false;
              this.show = true;
              this.makeOnlyBoardEmpty();
              this.clickable = true;
            }.bind(this),
            4000,
          );
        } else if (this.diff === "hard") {
          setTimeout(
            function() {
              this.show = false;
              this.show = true;
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
  },
};
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
