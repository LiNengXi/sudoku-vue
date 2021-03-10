<template>
    <div class="sudoku-wrap">
        <div class="sudoku" :class="[ isDone ? 's-done' : '']">
            <div class="title">Sudoku</div>

            <div class="grids-wrap">
                <ul v-for="(cells, i) in sudoku" :key="i" class="grids">
                    <li v-for="(cell, j) in cells" :key="j" class="cell">
                        <template v-if="typeof cell === 'number'">
                            {{ cell }}
                        </template>
                        <template v-else>
                            <input type="number" 
                                    :value="cell" 
                                    @keydown="keydownHandler(i, j, $event)"
                                    @keyup="keyupHandler(i, j, $event)"
                                    @change="changeHandler(i, j, $event)">
                        </template>
                    </li>
                </ul>
            </div>
        </div>

        <SudokuLevels :levels="levels" @takeLevel="chooseLevel" :difficulty="difficulty" />

        <SudokuTimeUse ref="TimeUse" />

        <button @click="restartSudoku">重新此局</button>
        <button @click="resetSudoku">新的一局</button>

        <SudokuNumbersCounter :sudoku="sudoku" />
    </div>
</template>

<script>
import SudokuCore from '../assets/SudokuCore.js';
let sudokuCore = new SudokuCore();

import SudokuTimeUse from './SudokuTimeUse';
import SudokuNumbersCounter from './SudokuNumbersCounter';
import SudokuLevels from './SudokuLevels';

const INITAL_DIFFICULTY = .5;

function copySudoku(sudoku) {
    return sudoku.map(rows => rows.map(ele => ele));
}

export default {
    data() {
        let sudoku = sudokuCore.createBlankCell(sudokuCore.initializeSudoku(), INITAL_DIFFICULTY);

        return {
            sudoku,
            initialSudoku: copySudoku(sudoku),
            date: '00 : 00',
            difficulty: INITAL_DIFFICULTY,
            prevDifficulty: INITAL_DIFFICULTY,
            levels: sudokuCore.levels,
            isDone: false
        };
    },
    mounted() {
        this.$refs.TimeUse.timeUse();
    },
    methods: {
        keydownHandler(i, j, $event) {
            let keyCode = $event.keyCode;

            if (keyCode === 8) {
                this.$set(this.sudoku[i], j, '');
            }

            if (keyCode === 116) {
                window.location.reload();
            }
        
            if ((keyCode >= 49 && keyCode <= 57) ||
                (keyCode >= 97 && keyCode <= 105)) {
                return;
            } else {
                $event.preventDefault();
            }
        },
        keyupHandler(i, j, $event) {
            let keyCode = $event.keyCode;

            if (keyCode === 229) {
                this.$set(this.sudoku[i], j, '');
            }

            $event.target.blur();
        },
        changeHandler(i, j, $event) {
            let val = $event.target.value.trim();

            val = val && val.slice(val.length - 1);

            this.$set(this.sudoku[i], j, val);

            let sudokuResult = this.sudoku.map(rows => rows.map(ele => parseInt(ele)));

            let isDone = sudokuCore.checkSudoku(sudokuResult);

            if (isDone) {
                this.$refs.TimeUse.clearTimeout();
                this.sudoku = sudokuResult;
                this.isDone = true;
            }
        },
        restartSudoku() {
            this.sudoku = copySudoku(this.initialSudoku);
            this.date = '00 : 00';
            this.difficulty = this.prevDifficulty;
            this.$refs.TimeUse.timeUse();
            this.isDone = false;
        },
        resetSudoku() {
            let sudoku = sudokuCore.createBlankCell(sudokuCore.initializeSudoku(), this.difficulty);
            
            this.sudoku = sudoku;
            this.initialSudoku = copySudoku(sudoku);
            this.date = '00 : 00';
            this.prevDifficulty = this.difficulty;
            this.$refs.TimeUse.timeUse();
            this.isDone = false;
        },
        chooseLevel(difficulty) {
            this.difficulty = difficulty;
        }
    },
    components: {
        SudokuTimeUse,
        SudokuNumbersCounter,
        SudokuLevels
    }
}
</script>

<style scoped>
.sudoku {
    overflow: hidden;
}
.sudoku.s-done {
    transition: transform .2s ease-out;
    transform: scale(.95);
}
.title {
    margin-bottom: -1px;
    border: 1px solid #ccc;
    width: 354px;
    font-size: 18px;
    line-height: 2;
}
.grids {
    overflow: hidden;
}
.cell {
    float: left;
    border: 1px solid #ccc;
    width: 40px;
    height: 40px;
    font-size: 16px;
    line-height: 40px;
}
.grids:not(:nth-child(3n+1)) {
    margin-top: -1px;
}
.cell:not(:nth-child(3n+1)) {
    margin-left: -1px;
}
.cell input {
    border: 0;
    width: 38px;
    height: 33px;
    font-size: 16px;
    text-align: center;
    color: #36f;
    background-color: #f7f7f7;
}
.cell input:focus {
    margin-bottom: -2px;
    border: 0;
    box-shadow: 0 0 10px #ccc;
    transform: translate(0, -2px) scale(1.15, 1.3);
}
button {
    margin-right: 20px;
    padding: 5px 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #fff;
    cursor: pointer;
    transition: transform .1s ease-out;
}
button:hover {
    transform: scale(.98) translate(0, 1px);
}
</style>