<script setup lang="ts">
import { computed, reactive, ref } from 'vue';


const board = reactive([
  ['', '', ''],
  ['', '', ''],
  ['', '', '']
]);

const winner = ref<string | null>(null);
const currentPlayer = ref<string>('X');
const isEndGame = computed(() =>  board.every(row => row.every(c => !!c)))


const playerMove = (rowIndex: number, colIndex: number) => {
  if (!winner.value && board[rowIndex][colIndex] === '') {
    board[rowIndex][colIndex] = currentPlayer.value;
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X';
  }

  const pWinner = calculateWinner(board)
  if ( pWinner){
    winner.value = pWinner
  }
};

//  easiest formula is to use a static pre-computed grid
const calculateWinner = (board: string[][]) => {
    const boardLength = board.length;

    //  So let's check them in all directions
    for( let i  = 0; i < boardLength; i++){
        // check columns, rows diagonal from i
        const rowStart = board[i][0]
        if(rowStart  && board[i].every(cell => cell === rowStart)){
            // perfect row
            return board[i][0]
        } 

        // column check
        const colStart = board[0][i]
        if(colStart && board.every(row => row[i] === colStart)){
            return board[i][i]
        }
    }

    // diagonals
    const leftDiag = board[0][0]
    if(leftDiag && board.every((row, i) => row[i] === leftDiag)){
        return leftDiag;
    }


    const n = boardLength - 1;
    // rever diagonal
    const rightDiag = board[n][n]
    if(rightDiag && board.every((row, i) => row[n-i] === rightDiag)){
        return leftDiag;
    }
}

const reset = () => {
    board.forEach(row => row.fill(''));
    winner.value = '';
    currentPlayer.value = 'X';
}

</script>

<template>
    <div class="game">
        <h2>Tik Tak Toe</h2>
        <div class="board">
            <div v-for="(row, rowIndex) in board" :key="rowIndex" class="row">
                <div v-for="(cell, colIndex) in row" :key="`${colIndex} ${rowIndex}`" class="cell" @click="playerMove(rowIndex, colIndex)">{{ cell }}</div>
            </div>
        </div>
        <div v-if="winner || isEndGame" class="result">
            <span v-if="winner">{{ winner }} wins!</span>
            <span v-else-if="isEndGame">Draw!</span>
            <button class="reset" @click="reset">Play again!</button>
        </div>
    </div>

</template>

<style scoped>
.game {
   padding: 40px 20px;
   display: flex  ;
    flex-direction: column;
    gap: 20px;
   justify-content: center ;
   align-items: center;
}

.board {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.row {
    display: flex;
    gap: 10px;
}

.cell {
    width: 50px;
    height: 50px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid rgba(0, 0, 0, 0.4);
    cursor: pointer;
}

.result {
    display: flex;
    flex-direction: column;
    gap: 8px;
}

.reset {
    cursor: pointer;
    border: none;
    background: transparent;
}

.reset:hover {
    text-decoration: underline;
}

</style>