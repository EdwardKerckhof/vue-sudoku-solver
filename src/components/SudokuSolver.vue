<template></template>

<script lang="ts">
import { defineComponent, onMounted } from 'vue'

export default defineComponent({
  name: 'SudokuSolver',

  setup() {
    const board1 = [
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
      [null, null, null, null, null, null, null, null, null],
    ]

    const boardInvalid = [
      [7, 8, null, 4, null, null, 1, 2, 0],
      [6, null, null, null, 7, 5, null, null, 9],
      [null, null, null, 6, null, 1, null, 7, 8],
      [null, null, 7, null, 4, null, 2, 6, 0],
      [null, null, 1, null, 5, null, 9, 3, 0],
      [9, null, 4, null, 6, null, null, null, 5],
      [null, 7, null, 3, null, null, null, 1, 2],
      [1, 2, null, null, null, 7, 4, null, 0],
      [null, 4, 9, 2, null, 6, null, null, 7],
    ]

    const wordlsHardestSudoku = [
      [8, null, null, null, null, null, null, null, null],
      [null, null, 3, 6, null, null, null, null, null],
      [null, 7, null, null, 9, null, 2, null, null],
      [null, 5, null, null, null, 7, null, null, null],
      [null, null, null, null, 4, 5, 7, null, null],
      [null, null, null, 1, null, null, null, 3, null],
      [null, null, 1, null, null, null, null, 6, 8],
      [null, null, 8, 5, null, null, null, 1, null],
      [null, 9, null, null, null, null, 4, null, null],
    ]

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

    const printBoardToConsole = (board: (number | null)[][]) => {
      let boardHash = ''
      board.forEach((row: any, i: number) => {
        boardHash += row.toString().replace(/,/g, ' ') + '\n'
      })
      console.log(boardHash)
    }

    onMounted(() => {
      // console.log('Invalid Sudoku:')
      // printBoardToConsole(boardInvalid)
      // if (solveBoard(boardInvalid)) {
      //   console.log('Solution:')
      //   printBoardToConsole(boardInvalid)
      // } else console.log('No solution possible!')
      // console.log('Empty Sudoku:')
      // printBoardToConsole(board1)
      // if (solveBoard(board1)) {
      //   console.log('Solution:')
      //   printBoardToConsole(board1)
      // } else console.log('No solution possible!')
      // console.log('Worlds hardest Sudoku:')
      // printBoardToConsole(wordlsHardestSudoku)
      // if (solveBoard(wordlsHardestSudoku)) {
      //   console.log('Solution:')
      //   printBoardToConsole(wordlsHardestSudoku)
      // } else console.log('No solution possible!')
    })

    return { board1 }
  },
})
</script>

<style lang="scss"></style>
