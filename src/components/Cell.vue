<script setup lang="ts">
const props = defineProps<{
    isOpened: boolean
    flagPlaced: boolean
    neighbors: number
    isBomb: boolean
    x: number
    y: number
}>()

const emit = defineEmits<{
    (e: 'bingNeighbors', x: number, y: number): void
    (e: 'toggleFlag', x: number, y: number): void
}>()

function handleCellClick() {
    if (!props.isOpened && !props.flagPlaced) {
        emit('bingNeighbors', props.x, props.y)
    }
}
function onRightClick() {
    console.log('right click')
    if (!props.isOpened) {
        emit('toggleFlag', props.x, props.y)
    }
}

</script>

<template>
    <div 
        class="single-cell-container" 
        :class="{ 
            mine: props.isBomb,
            zero: props.neighbors === 0 && !props.isBomb,
            one: props.neighbors === 1 && !props.isBomb,
            two: props.neighbors === 2 && !props.isBomb,
            three: props.neighbors === 3 && !props.isBomb,
            four: props.neighbors === 4 && !props.isBomb,
            five: props.neighbors === 5 && !props.isBomb,
            six: props.neighbors === 6 && !props.isBomb,
            seven: props.neighbors === 7 && !props.isBomb,
            eight: props.neighbors === 8 && !props.isBomb,
        }"
    >
        <button
            @contextmenu.prevent="onRightClick" 
            @click="handleCellClick"
            :class="{ 
                invisible: props.isOpened,
                flag: props.flagPlaced
            }"
        >
        </button>
    </div>
</template>

<style>
.single-cell-container button {
    width: 20px;
    height: 20px;
    background-color: darkgrey;
}

.single-cell-container {
    background-color: darkgrey;
    background-repeat: no-repeat;
    background-size: 20px 20px;
}

.mine {
    background-image: url(../assets/mine.png);
}

.gameover {
    background-image: url(../assets/mine.png);
    background-color: red;
}

.zero {
    background-image: url(../assets/0.png);
}

.one {
    background-image: url(../assets/1.png);
}

.two {
    background-image: url(../assets/2.png);
}

.three {
    background-image: url(../assets/3.png);
}

.four {
    background-image: url(../assets/4.png);
}

.five {
    background-image: url(../assets/5.png);
}

.six {
    background-image: url(../assets/6.png);
}

.seven {
    background-image: url(../assets/7.png);
}

.eight {
    background-image: url(../assets/8.png);
}

.flag {
    background-image: url(../assets/flag.png);
    background-size: 20px 20px;
    background-repeat: no-repeat;
    background-position: center; 
}

.invisible {
    visibility: hidden;
}
</style>
