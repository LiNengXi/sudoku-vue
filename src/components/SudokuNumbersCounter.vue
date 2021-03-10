<template>
    <ul class="numbers">
        <li v-for="(e, index) in numbers" :key="index" :class="[ e.isMax ? 's-max' : '' ]">{{ e.num }}</li>
    </ul>
</template>

<script>
const LEN = 9;

function initialNumbers() {
    let res = [];

    for (let i = 1; i <= LEN; i++) {
        res.push({
            num: i,
            isMax: false
        });
    }

    return res;
}

export default {
    props: ['sudoku'],
    data() {
        return {};
    },
    computed: {
        numbers() {
            let numbers = initialNumbers();

            for (let i = 0; i < LEN; i++) {
                let numsRes = JSON.stringify(this.sudoku).match(new RegExp(i + 1, 'g')),
                    len = numsRes ? numsRes.length : 0;
                numbers[i].isMax = len >= LEN;
            }

            return numbers;
        }
    }
}
</script>

<style scoped>
.numbers {
    display: flex;
    justify-content: space-around;
    margin-top: 10px;
    width: 360px;
    font-size: 18px;
}
.numbers .s-max {
    text-decoration: line-through;
}
</style>