<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps<{
    isBomb: boolean
    isOpened: boolean // no link with isOpened so far
    message: string
    x: number
    y: number
}>()

const emit = defineEmits<{
    (e: 'bingNeighbors', x: number, y: number): void
}>()

const isOpened = ref(false)
const open = () => (isOpened.value = true)
// const message = props.isBomb ? '💣' : '' // 💥

function handleCellClick() {
    emit('bingNeighbors', props.x, props.y)
    console.log(props.isBomb)
    console.log(props.x, props.y)
    open()
}
</script>

<template>
    <div class="single-cell-container">
        <button :disabled="isOpened" @click="handleCellClick"><p v-if="isOpened" >{{ props.message }}</p></button>
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
}
</style>