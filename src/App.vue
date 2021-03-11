<template>
  <h1>Two player tic tac toe</h1>
  <p>Score:</p>
  <p>
    {{ players[0].name }} : {{ players[0].points }} - {{ players[1].points }} :
    {{ players[1].name }}
  </p>
  <p v-if="!gameOver">It's player {{ symbol }}'s turn</p>
  <p v-else-if="isDraw">Draw!</p>
  <p v-else>Player {{ symbol }} won!</p>
  <div v-if="boardDimension >= 3 && boardDimension <= 10">
    <div v-for="(row, rowIndex) in board" :key="rowIndex">
      <button
        v-for="(place, placeIndex) in row"
        :key="placeIndex"
        :style="{
          width: 400 / boardDimension + 'px',
          height: 400 / boardDimension + 'px',
          fontSize: 200 / boardDimension + 'px',
          border: 10 / boardDimension + 'px solid wheat',
        }"
        :disabled="gameOver"
        @click="occupyPlace(rowIndex, placeIndex)"
        class="button-style"
      >
        {{ board[rowIndex][placeIndex] }}
      </button>
    </div>
  </div>
  <p>
    Enter board dimension (3 is default and min dimension 10 is max dimension)
  </p>
  <!-- Dont want v-model since the boarDimension should not be able to change midgame -->
  <input v-model="inputVal" />
  <button id="newGameBtn" @click="newGame">New game</button>
  <p v-if="boardDimension < 3 || isNaN(boardDimension) || boardDimension > 10">
    Please enter a valid value of between 3 and 10
  </p>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      inputVal: "3",
      //Three by default
      boardDimension: 3,
      players: [
        {
          name: "Circles",
          points: 0,
        },
        {
          name: "Crosses",
          points: 0,
        },
      ],
      isCirclesTurn: true,
      isDraw: false,
      gameOver: false,
      board: [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ],
    };
  },
  methods: {
    occupyPlace(rowIndex, placeIndex) {
      //Returning if place is already taken
      if (this.board[rowIndex][placeIndex] !== "") return;
      this.board[rowIndex][placeIndex] = this.symbol;
      //Have to check for win before turning change whos turn it is
      if (this.checkForWin()) {
        this.gameOver = true;
        this.players[this.isCirclesTurn ? 0 : 1].points++;
      } else if (this.checkForEndOfGame()) {
        this.isDraw = true;
        this.gameOver = true;
      } else this.isCirclesTurn = !this.isCirclesTurn;
    },
    checkRows() {
      //Cannot return from forEach therefore using for
      for (let i = 0; i < this.boardDimension; i++) {
        if (
          this.board[i].filter((place) => place === this.symbol).length ===
          this.boardDimension
        )
          return true;
      }
      return false;
    },
    checkColums() {
      for (let i = 0; i < this.boardDimension; i++) {
        let count = 0;
        for (let j = 0; j < this.boardDimension; j++) {
          if (this.board[j][i] === this.symbol) count++;
        }
        if (count === this.boardDimension) return true;
      }
      return false;
    },
    checkCross() {
      let startOfRowCount = 0;
      let endOfRowCount = 0;
      for (let i = 0; i < this.boardDimension; i++) {
        if (this.board[i][startOfRowCount] === this.symbol) startOfRowCount++;
        if (
          this.board[i][this.board.length - 1 - endOfRowCount] === this.symbol
        )
          endOfRowCount++;
      }
      return (
        startOfRowCount === this.boardDimension ||
        endOfRowCount === this.boardDimension
      );
    },
    checkForWin() {
      return this.checkRows() || this.checkColums() || this.checkCross();
    },
    checkForEndOfGame() {
      return (
        this.board.filter(
          (row) =>
            row.filter((place) => place !== "").length === this.boardDimension
        ).length === this.boardDimension
      );
    },
    newGame() {
      //Only converting here so that we avoid having the board dimension change midgame
      this.boardDimension = parseInt(this.inputVal);
      if (
        isNaN(this.boardDimension) ||
        this.boardDimension < 3 ||
        this.boardDimension > 10
      )
        return;
      let b = [];
      for (let i = 0; i < this.boardDimension; i++) {
        let row = [];
        for (let j = 0; j < this.boardDimension; j++) {
          row.push("");
        }
        b.push(row);
      }
      this.board = b;
      this.isCirclesTurn = true;
      this.gameOver = false;
      this.isDraw = false;
    },
  },
  computed: {
    symbol() {
      return this.isCirclesTurn ? "O" : "X";
    },
  },
};
</script>

<style>
body {
  background-color: black;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: wheat;
  margin-top: 60px;
  background-color: black;
}

.row-style {
  display: inline;
}

.button-style {
  margin: 0px;
  text-align: center;
  background-color: black;
  color: wheat;
  padding: 0px;
  box-sizing: border-box;
  /*Added to hinder the buttons moving when their content goes from "" to "O" or "X"*/
  vertical-align: top;
  display: inline;
}

#newGameBtn,
input {
  font-size: 25px;
  background-color: black;
  border: 3px solid wheat;
  color: wheat;
}

input {
  padding: 0px 3px;
  width: 30%;
}

p {
  font-size: 20px;
}
</style>
