<template>
  <div id="app">
    <svg @click="setPlayerType('X')"><use xlink:href="#X"/></svg>
    <svg @click="setPlayerType('O')"><use xlink:href="#O"/></svg>
    <h1 v-if="winner">{{winner}}</h1>
    <div class="game-grid">
      <button v-for="(square,index) in board"
              @click="huPicks(index)"
              :disabled="square !== index"
              :key="square+index"
              v-html="notOrCross(square, index)"></button>
    </div>
  </div>
</template>

<script>
import Minimax from 'tic-tac-toe-minimax';
const { GameStep } = Minimax;

export default {
  data () {
    return {
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
      this.board.splice(index, 1, this.symbols.huPlayer);
      this.currentPlayer = 'computer';
    },
    aiPicks: function() {
      const gameStep = GameStep( this.board, this.symbols, this.difficulty );

      // these ones needed for when AI plays first.
      const fullBoard = /(X|O){9}/.test(gameStep.board.join(''));
      const emptyBoard = /\d{9}/.test(gameStep.board.join(''));

      if (gameStep.winner !== null) {
        console.log('yes');
        this.winner = gameStep.winner;
        this.resetBoard();
      } else if (fullBoard || emptyBoard) {
        this.winner = 'draw';
        this.resetBoard();
      } else {
        this.winner = gameStep.winner;
        console.log('all good',gameStep);
        this.board = this.board.map((item,index) => {
          return gameStep.board[index];
        });
      }
      this.currentPlayer = 'human';
    },
    resetBoard: function() {
      this.board = this.board.map((item,index) => {
        return index;
      });
    },
    setPlayerType(type){
      if(type === 'X'){
        this.symbols = {
          huPlayer: 'X',
          aiPlayer: 'O'
        }
      } else {
        this.symbols = {
          huPlayer: 'O',
          aiPlayer: 'X'
        }
        this.currentPlayer = 'computer';
      }
    },
    notOrCross: function(text, index){ // why u no comuted?
      return text === index ? '' : '<svg><use xlink:href="#'+text+'"/></svg>'
    }
  },
  computed: {

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

  svg {
    width: 30px; height: 30px;
  }
}
</style>
