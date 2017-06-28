<template>
  <div id="app">
    <button @click="currentPlayer = currentPlayer === 'human' ? 'computer' : 'human'">switch to other</button>
    <h1 v-if="winner">{{winner}}</h1>
    <div class="game-grid">
      <button v-for="(square,index) in board"
              @click="changeItem(index)"
              :disabled="square !== index"
              :key="square+index">{{square === index ? '' : square}}</button>
    </div>
  </div>
</template>

<script>
import Minimax from 'tic-tac-toe-minimax';
const { GameStep } = Minimax;

export default {
  data () {
    return {
      // huPlayer: "X",
      // aiPlayer: "O",
      winner: null,
      currentPlayer: 'human',
      symbols: {
          huPlayer: 'X',
          aiPlayer: 'O'
      },
      difficulty: "Hard",
      board: [0,1,2,
              3,4,5,
              6,7,8]
    }
  },
  methods: {
    changeItem: function(item){
      this.board.splice(item, 1, 'X');
      this.currentPlayer = 'computer';
    }
  },
  watch: {
    currentPlayer(value){
      if (value === 'computer') {
        const gameStep = GameStep( this.board, this.symbols, this.difficulty );
        this.board = this.board.map((item,index) => {
          return gameStep.board[index];
        });
        this.winner = gameStep.winner;
        this.currentPlayer = 'human';
      }
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.game-grid{
  margin: 0 auto;
  width: 150px;

  display: flex;
  flex-flow:row wrap;
}

button {
  width: 45px; height: 45px;
}
</style>
