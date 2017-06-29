<template>
  <div id="app">
    <div class="modal" v-show="displayModal">
      <h1 v-if="winner">{{winner}}</h1>
      <div class="win-display" v-if="winner">
        <div class="grid-piece"
             v-for="(square,index) in board"
             :key="square+index"
              v-html="notOrCross(square, index)"></div>
      </div>
      <h2>{{winner ? 'Play again?' : "Play as:"}}</h2>
      <svg @click="setPlayerType('X')"><use xlink:href="#X"/></svg>
      <svg @click="setPlayerType('O')"><use xlink:href="#O"/></svg>
    </div>

    <div class="game-grid" v-show="!displayModal">
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
      displayModal: true,
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
        this.winner = gameStep.winner === 'aiPlayer' ? 'Computer wins!' : 'You win!';
        this.updateBoard(gameStep.board); // keep updating this.board so we can display game thumbnail.
        this.endGame();
      } else if (emptyBoard) { // we don't need to update this.board if human plays first to a draw
        this.winner = 'It was a draw';
        this.endGame();
      } else if (fullBoard) {
        this.winner = 'It was a draw';
        this.updateBoard(gameStep.board);
        this.endGame();
      } else {
        this.winner = null;
        this.updateBoard(gameStep.board);
      }
      this.currentPlayer = 'human';
    },
    endGame: function() {
      this.displayModal = true;
    },
    updateBoard: function(newArr) {
      this.board = this.board.map((item,index) => {
        return newArr[index];
      });
    },
    setPlayerType(type){
      this.board = this.board.map((item,index) => {
        return index;
      });
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
      this.displayModal = false;
    },
    notOrCross: function(text, index){ // why u no comuted?
      return text === index ? '' : '<svg><use xlink:href="#'+text+'"/></svg>'
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

.win-display{
  margin: 0 auto;
  width: 150px;

  display: flex;
  flex-flow:row wrap;

  .grid-piece {
    width: 45px; height: 45px;
    border: 1px solid #ddd;

    svg {
      width: 30px; height: 30px;
      margin-top: 7px;
    }
  }
}

.game-grid{
  margin: 0 auto;
  width: 300px;

  display: flex;
  flex-flow:row wrap;
}

button {
  width: 95px; height: 95px;

  svg {
    width: 80px; height: 80px;
  }
}
</style>
