<script setup lang="ts">
const boardSize = 8

const board = ref(Array(boardSize).fill(null).map(() => Array(boardSize).fill(null)))
const currentPlayer = ref("black")
const canMove = ref(true)
const passes = ref(0)

const initializeBoard = () => {
  for(let i = 0; i < boardSize; i++) {
    for(let j = 0; j < boardSize; j++) {
      board.value[i][j] = null
    }
  }

  // Add this line to reset the board
  board.value = Array(boardSize).fill(null).map(() => Array(boardSize).fill(null))

  board.value[3][3] = "white"
  board.value[3][4] = "black"
  board.value[4][3] = "black"
  board.value[4][4] = "white"
}

initializeBoard()

const placePiece = (row:any, col:any) => {
  if (board.value[row][col]) return

  const directions = [
    [-1, 0],
    [1, 0],
    [0, -1],
    [0, 1],
    [-1, -1],
    [-1, 1],
    [1, -1],
    [1, 1],
  ]

  let flipped = false
  for (const [dr, dc] of directions) {
    let r = row + dr
    let c = col + dc
    let toFlip = []

    while (r >= 0 && r < boardSize && c >= 0 && c < boardSize) {
      const cell = board.value[r][c]

      if (!cell) break;
      if (cell === currentPlayer.value) {
        flipped = flipped || toFlip.length > 0
        toFlip.forEach(([fr, fc]) => {
          board.value[fr][fc] = currentPlayer.value
        })
        break
      }

      toFlip.push([r, c])
      r += dr
      c += dc
    }
  }

  if (flipped) {
    board.value[row][col] = currentPlayer.value
    passes.value = 0
    currentPlayer.value = currentPlayer.value === "black" ? "white" : "black"
  } else {
    canMove.value = false
  }
}

const onResetGame = () => {
  initializeBoard()
  currentPlayer.value = "black"
  passes.value = 0
  canMove.value = true
}

</script>

<template>
  <div class="flex flex-col items-center bg-slate-400">
    <div class="text-lg my-4">
      Current Player: <span :class="{'text-black': currentPlayer === 'black', 'text-white': currentPlayer === 'white'}">{{ currentPlayer }}</span>
    </div>
    <div class="bg-gray-800">
      <div v-for="(row, rowIndex) in board" class="flex">
        <div
          v-for="(cell, colIndex) in row"
          @click="placePiece(rowIndex, colIndex)"
          class="w-12 h-12 border border-gray-900 flex justify-center items-center"
        >
          <div v-if="cell" :class="{'bg-black': cell === 'black', 'bg-white': cell === 'white'}" class="w-8 h-8
          rounded-full"></div>
        </div>
      </div>
    </div>
    <div class="my-4">
      <button @click="onResetGame" class="bg-blue-500 text-white px-4 py-2 rounded">Reset Game</button>
    </div>
  </div>
</template>

<style scoped>
</style>
