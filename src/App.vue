<template>
  <div id="app">
    <button @click="currentPlayer = currentPlayer === 'human' ? 'computer' : 'human'">switch to other</button>
    <h1 v-if="winner">{{winner}}</h1>
    <div class="game-grid">
      <button v-for="(square,index) in board"
              @click="huPicks(index)"
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
    huPicks: function(index){
      this.board.splice(index, 1, 'X');
      this.currentPlayer = 'computer';
    },
    aiPicks: function() {
      const gameStep = GameStep( this.board, this.symbols, this.difficulty );
      console.log(GameStep( ["O", "O", "X", "X", "X", "O", "O", "X", "O"], this.symbols, this.difficulty ));
      if (gameStep.board.includes(this.symbols.huPlayer) || gameStep.board.includes(this.symbols.aiPlayer) && gameStep.winner === null) {
        console.log('all good',gameStep);
        this.winner = gameStep.winner;
        this.board = this.board.map((item,index) => {
          return gameStep.board[index];
        });
      } else if (gameStep.board.includes(this.symbols.huPlayer) || gameStep.board.includes(this.symbols.aiPlayer) && gameStep.winner !== null){
        this.winner = gameStep.winner;
        this.board = this.board.map((item,index) => {
          return index;
        });
        console.log('Winner',this.board);
      } else {
        console.log('draw',gameStep);
        this.winner = 'draw';
        this.board = this.board.map((item,index) => {
          return index;
        });
      }
      this.currentPlayer = 'human';
    }
  },
  watch: {
    currentPlayer(value){
      if (value === 'computer') {
        this.aiPicks();
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
