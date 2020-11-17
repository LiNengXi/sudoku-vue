<template>
    <div class="sudoku-wrap">
        <div class="sudoku" :class="[ isDone ? 's-done' : '']">
            <div class="title">Sudoku</div>

            <div class="grids-wrap">
                <ul v-for="(cells, i) in sudoku" :key="i" class="grids">
                    <li v-for="(cell, j) in cells" :key="j" class="cell">
                        <input type="text" 
                                :value="typeof cell === 'object' ? cell.value : cell" 
                                :disabled="typeof cell === 'object' ? cell.disabled : true"
                                @keydown="keydownHandler(i, j, $event)"
                                @keyup="keyupHandler(i, j, $event)"
                                @change="changeHandler(i, j, $event)">
                    </li>
                </ul>
            </div>
        </div>

        <SudokuLevels :levels="levels" @takeLevel="chooseLevel" :difficulty="difficulty" />

        <SudokuTimeUse ref="TimeUse" />

        <button @click="restartSudoku">重新此局</button>
        <button @click="resetSudoku">新的一局</button>

        <SudokuNumbersCounter :sudoku="sudoku" ref="numbersCounter" />
    </div>
</template>

<script>
import SudokuCore from '../assets/SudokuCore.js';
let sudokuCore = new SudokuCore();

import SudokuTimeUse from './SudokuTimeUse';
import SudokuNumbersCounter from './SudokuNumbersCounter';
import SudokuLevels from './SudokuLevels';

const INITAL_DIFFICULTY = .5;
const LEN = 9;

function copyBlankSudoku(sudoku) {
    let sudokuRes = [];
    
    for (let i = 0; i < LEN; i++) {
        let tmp = [];
        for (let j = 0; j < LEN; j++) {
            tmp.push(sudoku[i][j]);
        }
        sudokuRes.push(tmp);
    }

    return sudokuRes;
}

export default {
    data() {
        let sudoku = this.createBlankCell(sudokuCore.initializeSudoku(), INITAL_DIFFICULTY);

        return {
            sudoku,
            initialSudoku: copyBlankSudoku(sudoku),
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
        createBlankCell(sudoku, difficulty) {
            for (let i = 0; i < LEN; i++) {
                for (let j = 0; j < LEN; j++) {
                    if (Math.random() > difficulty) {
                        sudoku[i][j] = {
                            value: '',
                            disabled: false
                        };
                    }
                }
            }

            return sudoku;
        },
        keydownHandler(i, j, $event) {
            let keyCode = $event.keyCode;

            if (keyCode === 8) {
                $event.target.value = '';
                this.$set(this.sudoku[i], j, { value: '', disabled: false });
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
                $event.target.value = '';
                this.$set(this.sudoku[i], j, { value: '', disabled: false });
            }

            $event.target.blur();
        },
        changeHandler(i, j, $event) {
            let val = $event.target.value.trim();

            val = val && parseInt(val.slice(val.length - 1));

            $event.target.value = val;
            this.$set(this.sudoku[i], j, { value: val, disabled: false });

            let sudoku = this.sudoku,
                sudokuRes = [];

            for (let i = 0; i < LEN; i++) {
                let tmp = [];
                for (let j = 0; j < LEN; j++) {
                    let item = sudoku[i][j];

                    tmp.push(typeof item === 'object' ? item.value : item);
                }
                sudokuRes.push(tmp);
            }

            let isDone = sudokuCore.checkSudoku(sudokuRes);

            if (isDone) {
                for (let i = 0; i < LEN; i++) {
                    for (let j = 0; j < LEN; j++) {
                        if (typeof sudoku[i][j] === 'object') {
                            this.$set(this.sudoku[i], j, Object.assign(sudoku[i][j], { disabled: true }));
                        }
                    }
                }
                this.$refs.TimeUse.clearTimeout();
                this.isDone = true;
            }
        },
        restartSudoku() {
            this.sudoku = copyBlankSudoku(this.initialSudoku);
            this.date = '00 : 00';
            this.difficulty = this.prevDifficulty;
            this.$refs.TimeUse.timeUse();
            this.isDone = false;
        },
        resetSudoku() {
            let sudoku = this.createBlankCell(sudokuCore.initializeSudoku(), this.difficulty);
            
            this.sudoku = sudoku;
            this.initialSudoku = copyBlankSudoku(sudoku);
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