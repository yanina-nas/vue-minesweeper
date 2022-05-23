<script setup lang="ts">

const props = defineProps<{
    isBomb: boolean
    isOpened: boolean
    message: string
    neighbors: number
    x: number
    y: number
}>()

const emit = defineEmits<{
    (e: 'bingNeighbors', x: number, y: number): void
}>()

function handleCellClick() {
    emit('bingNeighbors', props.x, props.y)
}
</script>

<template>
    <div class="single-cell-container">
        <button :disabled="isOpened" @click="handleCellClick"><p v-if="props.isOpened" >{{ props.neighbors !== 0 ? props.neighbors : props.message }}</p></button>
    </div>
</template>

<style>
.single-cell-container button {
    width: 20px;
    height: 20px;
    background-color: teal;
}

button:disabled {
    background-color: grey;
    color: black;
}
</style>