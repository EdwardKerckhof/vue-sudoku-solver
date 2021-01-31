<template>
  <h1>Sudoku Solver</h1>
  <p>Using the backtracking algorithm</p>
  <hr />
  <sudoku-solver />
  <app-footer />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import AppFooter from './components/Footer.vue'
import SudokuSolver from './components/SudokuSolver.vue'

export default defineComponent({
  name: 'App',

  components: {
    SudokuSolver,
    AppFooter,
  },
})
</script>

<style lang="scss">
body {
  min-height: 100vh;
}

#app {
  height: calc(100vh);
  position: relative;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  line-height: 1.4;
  letter-spacing: 0.5px;
  color: #2c3e50;
  padding-top: 60px;

  h1 {
    margin-bottom: 10px;
  }

  p {
    font-style: italic;
  }

  hr {
    width: 30%;
    margin: 50px auto;
  }
}
</style>
