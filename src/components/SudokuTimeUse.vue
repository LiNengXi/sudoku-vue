<template>
    <div class="time">{{ date }}</div>
</template>

<script>
function fixZero(num) {
    return num < 10 ? `0${ num }` : num;
}

export default {
    data() {
        return {
            date: '00 : 00'
        };
    },
    mounted() {
        this.timeUse();
    },
    methods: {
        timeUse() {
            this.clearTimeout();
            this.startTime = Date.now();
            this.counterDown();
        },
        counterDown() {
            let now = Date.now(),
                timeDiff = now - this.startTime,
                h = parseInt(timeDiff / 1000 / 60 / 60 % 24),
                m = parseInt(timeDiff / 1000 / 60 % 60),
                s = parseInt(timeDiff / 1000 % 60);
            
            this.date = `${ !h ? '' : fixZero(h) + ' : ' }${ fixZero(m) } : ${ fixZero(s) }`

            this.timerID = setTimeout(() => {
                this.counterDown();
            }, 1000);
        },
        clearTimeout() {
            clearTimeout(this.timerID);
        }
    }
}
</script>

<style scoped>
.time {
    margin: 10px 0;
    width: 360px;
    font-size: 16px;
    line-height: 1.5;
    color: #333;
}
</style>