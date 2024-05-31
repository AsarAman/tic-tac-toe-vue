<template>
  <div class="game">
    <div class="settings">
      <label>
        Grid Size:
        <input type="number" v-model.number="gridSize" min="3" max="10" />
      </label>
      <label>
        Number of Players:
        <input type="number" v-model.number="numPlayers" min="2" max="10" />
      </label>
      <button @click="startGame">Start Game</button>
    </div>
    <div v-if="gameStarted" :style="boardStyle" class="board">
      <div
        v-for="(cell, index) in board"
        :key="index"
        class="cell"
        @click="makeMove(index)"
      >
        {{ cell }}
      </div>
    </div>
    <div class="status">{{ status }}</div>
    <button v-if="gameStarted" @click="resetGame">Reset</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gridSize: 3,
      numPlayers: 2,
      board: [],
      currentPlayer: 0,
      players: [],
      gameStarted: false,
      winner: null,
    };
  },
  computed: {
    status() {
      if (this.winner !== null) {
        return `Winner: Player ${this.winner + 1}`;
      } else if (!this.board.includes(null)) {
        return "It's a draw!";
      } else {
        return `Next player: Player ${this.currentPlayer + 1}`;
      }
    },
    boardStyle() {
      return {
        display: 'grid',
        gridTemplateColumns: `repeat(${this.gridSize}, 50px)`,
        gridTemplateRows: `repeat(${this.gridSize}, 50px)`,
        gap: '5px',
      };
    },
  },
  methods: {
    startGame() {
      this.board = Array(this.gridSize * this.gridSize).fill(null);
      this.players = Array.from({ length: this.numPlayers }, (_, i) => `Player ${i + 1}`);
      this.currentPlayer = 0;
      this.winner = null;
      this.gameStarted = true;
    },
    makeMove(index) {
      if (!this.board[index] && this.winner === null) {
        // Directly update the board array
        this.board.splice(index, 1, this.players[this.currentPlayer]);
        if (this.checkWinner()) {
          this.winner = this.currentPlayer;
        } else {
          this.currentPlayer = (this.currentPlayer + 1) % this.numPlayers;
        }
      }
    },
    checkWinner() {
      const size = this.gridSize;
      const lines = [];

      // Rows and Columns
      for (let i = 0; i < size; i++) {
        lines.push(this.board.slice(i * size, i * size + size));
        lines.push(this.board.filter((_, idx) => idx % size === i));
      }

      // Diagonals
      lines.push(this.board.filter((_, idx) => idx % (size + 1) === 0));
      lines.push(this.board.filter((_, idx) => idx % (size - 1) === 0 && idx !== 0 && idx !== size * size - 1));

      return lines.some(line => line.every(cell => cell && cell === line[0]));
    },
    resetGame() {
      this.gameStarted = false;
      this.board = [];
    },
  },
};
</script>

<style scoped>
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.settings {
  margin-bottom: 20px;
}
.board {
  display: grid;
  gap: 5px;
}
.cell {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 50px;
  height: 50px;
  background-color: #f0f0f0;
  font-size: 1rem;
  cursor: pointer;
}
.status {
  margin: 20px;
  font-size: 1.5rem;
}
button {
  padding: 10px 20px;
  font-size: 1rem;
}
</style>
