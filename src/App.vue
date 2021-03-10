<template>
  <div class="app">
    <Sudoku />
  </div>
</template>

<script>
import Sudoku from './components/Sudoku.vue'

export default {
  name: 'App',
  components: {
    Sudoku
  }
}
</script>

<style>
* {
    margin: 0;
    box-sizing: border-box;
    padding: 0;
}
.app {
    /* font-family: Avenir, Helvetica, Arial, sans-serif; */
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    margin: 20px auto;
    width: 360px;
    font-size: 14px;
    color: #36f;
    text-align: center;
    user-select: none;
}
input,
button {
    outline: none;
}
li {
    list-style-type: none;
}

body {
    background-color: #f7f7f7;
}
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  appearance: none; 
  margin: 0; 
}
/* 火狐 */
input{
  -moz-appearance: textfield;
}
</style>
