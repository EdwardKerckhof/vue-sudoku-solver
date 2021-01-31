<template>
  <div class="container">
    <form id="sudokuBoardForm" @submit.prevent="solve">
      <button type="submit">{{ buttonText }}</button>
      <button @click.prevent="resetBoard">Reset</button>

      <div id="sudokuBoard">
        <input
          v-for="i in 81"
          :key="i"
          :ref="
            (el) => {
              inputs[i] = el
            }
          "
          type="number"
          maxlength="1"
          max="9"
          min="1"
        />
      </div>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'SudokuSolver',

  setup() {
    const inputs = ref<number[]>([])
    const buttonText = ref('Solve')

    const rowsValid = (board: (number | null)[][]) => {
      // checks if any of the rows contain a 0
      // if so => return false
      for (let i = 0; i < board.length; i++) {
        let cur: number[] = []
        for (let j = 0; j < board[0].length; j++) {
          if (cur.includes(board[i][j] as number)) return false
          else if (board[i][j] !== null) cur.push(board[i][j] as number)
        }
      }
      return true
    }

    const columsValid = (board: (number | null)[][]) => {
      // checks if any of the colums contain a 0
      // if so => return false
      for (let i = 0; i < board.length; i++) {
        let cur: number[] = []
        for (let j = 0; j < board[0].length; j++) {
          if (cur.includes(board[j][i] as number)) return false
          else if (board[j][i] !== null) cur.push(board[j][i] as number)
        }
      }
      return true
    }

    const boxValid = (board: (number | null)[][]) => {
      // check boxes 3x3
      // e.g:
      //     (0)      (1)    (2)
      //    7 8 0 | 4 0 0 | 1 2 0
      //    6 0 0 | 0 7 5 | 0 0 9   (0)
      //    0 0 0 | 6 0 1 | 0 7 8
      //    ---------------------
      //    0 0 7 | 0 4 0 | 2 6 0
      //    0 0 1 | 0 5 0 | 9 3 0   (1)
      //    9 0 4 | 0 6 0 | 0 0 5
      //    ---------------------
      //    0 2 0 | 3 0 0 | 0 1 2
      //    1 2 0 | 0 0 4 | 4 0 9   (2)
      //    0 4 9 | 2 0 0 | 0 0 7
      const boxCoordinates = [
        [0, 0],
        [0, 1],
        [0, 2],
        [1, 0],
        [1, 1],
        [1, 2],
        [2, 0],
        [2, 1],
        [2, 2],
      ]

      // += 3 -> go from top to bottom 3 boxes at a time
      for (let y = 0; y < board.length; y += 3) {
        // += 3 -> go from left to right 3 boxes at a time
        for (let x = 0; x < board[0].length; x += 3) {
          // check if one of those boxes contain a duplicate
          let cur: number[] = []
          for (let i = 0; i < board.length; i++) {
            let coords = [...boxCoordinates[i]]
            coords[0] += y
            coords[1] += x

            if (cur.includes(board[coords[0]][coords[1]] as number))
              return false
            else if (board[coords[0]][coords[1]] !== null)
              cur.push(board[coords[0]][coords[1]] as number)
          }
        }
      }

      return true
    }

    const isBoardValid = (board: (number | null)[][]): boolean => {
      return rowsValid(board) && columsValid(board) && boxValid(board)
    }

    const findFirstEmptySquare = (board: (number | null)[][]) => {
      for (let i = 0; i < board.length; i++) {
        for (let j = 0; j < board[0].length; j++) {
          if (board[i][j] === null) return [i, j] // return column (y), row (x), not x, y
        }
      }
      return false
    }

    const solveBoard = (board: (number | null)[][]) => {
      let find = findFirstEmptySquare(board)

      if (!find) return true
      else {
        const y = find[0]
        const x = find[1]

        // try each number from 1-9
        for (let i = 1; i < 10; i++) {
          if (isBoardValid(board)) {
            let newBoard = board
            newBoard[y][x] = i

            if (solveBoard(newBoard)) return true

            newBoard[y][x] = (null as unknown) as number
          }
        }

        return false
      }
    }

    const updateBoard = (board: number[][]) => {
      let userInputs: Array<string> = []
      for (let i = 1; i <= board.length; i++) {
        for (let j = 0; j < board[0].length; j++) {
          userInputs.push(String(board[i - 1][j]))
        }
      }

      inputs.value.forEach((input, i) => {
        ;((input as unknown) as HTMLInputElement).value = userInputs[i - 1]
      })
    }

    const solve = () => {
      buttonText.value = 'Solving...'

      let userBoard: Array<number[]> = [[]]
      let j = 0
      for (let i = 1; i <= 81; i++) {
        const input = ((inputs.value[i] as unknown) as HTMLInputElement).value

        if (((input as unknown) as string) === '')
          userBoard[j].push((null as unknown) as number)
        else userBoard[j].push(Number(input))

        if (i % 9 === 0 && i < 81) {
          userBoard.push([])
          j++
        }
      }
      console.log(userBoard)

      if (solveBoard(userBoard)) {
        updateBoard(userBoard)
        buttonText.value = 'Solved!'
        console.log('Solution:')
        printBoardToConsole(userBoard)
      } else {
        buttonText.value = 'Impossible to solve!'
      }
    }

    const resetBoard = () => {
      inputs.value.forEach((input) => {
        ;((input as unknown) as HTMLInputElement).value = ''
      })
      buttonText.value = 'Solve'
    }

    const printBoardToConsole = (board: (number | null)[][]) => {
      let boardHash = ''
      board.forEach((row: any) => {
        boardHash += row.toString().replace(/,/g, ' ') + '\n'
      })
      console.log(boardHash)
    }

    return { solve, inputs, resetBoard, buttonText }
  },
})
</script>

<style lang="scss" scoped>
$red: #dc3545;
$green: #28a745;

.container {
  display: flex;
  justify-content: center;

  #sudokuBoard {
    display: grid;
    grid-template-columns: repeat(9, 40px);

    input {
      height: 40px;
      text-align: center;
      font-size: 16px;
    }
    /* Chrome, Safari, Edge, Opera */
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }

    /* Firefox */
    input[type='number'] {
      -moz-appearance: textfield;
    }
  }

  button {
    cursor: pointer;
    margin: 0 10px 20px 10px;
    background: none;
    border: 2px solid $red;
    color: $red;
    padding: 8px 15px;
    border-radius: 8px;
    transition: all 0.2s ease;
    outline: none;

    &:hover {
      background: $red;
      color: white;
    }
  }

  button[type='submit'] {
    border-color: $green;
    color: $green;

    &:hover {
      background: $green;
      color: white;
    }
  }
}
</style>
