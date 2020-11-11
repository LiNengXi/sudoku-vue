<template>
    <ul class="levels">
        <li v-for="(item, index) in levels" :key="index"
            :class="[ index === curIndex ? 's-current' : 'n-current' ]"
            @click="chooseLevel(index, $event)">
            {{ item.text }}
        </li>
    </ul>
</template>

<script>
export default {
    props: ['levels', 'difficulty'],
    data() {
        return {
            curIndex: 0
        };
    },
    methods: {
        chooseLevel(index) {
            this.curIndex = index;
            this.$emit('takeLevel', this.levels[index].difficulty);
        }
    },
    watch: {
        difficulty: function (newVal) {
            for (let i = 0, len = this.levels.length; i < len; i++) {
                if (this.levels[i].difficulty === newVal) {
                    this.curIndex = i;
                }
            }
        }
    }
}
</script>

<style scoped>
.levels {
    display: flex;
    justify-content: space-around;
    margin-top: 10px;
    width: 360px;
    color: #333;
}
.levels li {
    cursor: pointer;
}
.levels li.s-current {
    font-weight: 700;
    color: lightcoral;
}
.levels li.n-current {
    opacity: .6;
}
</style>