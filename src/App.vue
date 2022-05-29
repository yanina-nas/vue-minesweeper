<script setup lang="ts">
import Cell from './components/Cell.vue'
// import Game from './components/Game.vue'
import { ref, onUpdated, watchEffect } from 'vue'


// game setup. for now hardcoded
const DEFAULT_ROWS = 10
const DEFAULT_COLUMNS = 10
const DEFAULT_MINES = 10

interface Minefield {
  isOpened: boolean
  isBomb: boolean
  flagPlaced: boolean
  message: string
  neighbors: number
  gameover: boolean
}

const onBingNeighbors = (x: number, y: number) => {
  updateMinefield(state.value, x, y)
  console.log('cell click : x ', x, ', y ', y)
}

const onToggleFlag = (x: number, y: number) => {
  toggleFlag(state.value, x, y)
  // on placed flag `mines not guessed` value must be updated
}

const toggleFlag = (minefield: Minefield[][], x: number, y: number) => {
  minefield[x][y].flagPlaced = !minefield[x][y].flagPlaced
  console.log('flag toggle : x ', x, ', y ', y)
}

const updateMinefield = (minefield: Minefield[][], x: number, y: number) => {
  minefield[x][y].isOpened = true
  // handle gameover
  if (minefield[x][y].isBomb) {
    minefield[x][y].gameover = true
    for(let indexX = 0; indexX < minefield.length; indexX++) {
      for(let indexY = 0; indexY < minefield[0].length; indexY++) {
        minefield[indexX][indexY].isOpened = true
      }
    }
    return
  }

  // go down x 0-9
  for (let downIndex = x; downIndex < 10; downIndex++) {
    if (minefield[downIndex][y].flagPlaced)
      break
    minefield[downIndex][y].isOpened = true
    if (minefield[downIndex][y].neighbors !== 0)
      break
  }
  // go up x 9-0
  for (let upIndex = x; upIndex >= 0; upIndex--) {
    if (minefield[upIndex][y].flagPlaced)
      break
    minefield[upIndex][y].isOpened = true
    if (minefield[upIndex][y].neighbors !== 0)
      break
  }
  // go left y 9-0
  for (let leftIndex = y; leftIndex >= 0; leftIndex--) {
    if (minefield[x][leftIndex].flagPlaced)
      break
    minefield[x][leftIndex].isOpened = true
    if (minefield[x][leftIndex].neighbors !== 0)
      break
  }
  // go right y 0-9
  for (let rightIndex = y; rightIndex < 10; rightIndex++) {
    if (minefield[x][rightIndex].flagPlaced)
      break
    minefield[x][rightIndex].isOpened = true
    if (minefield[x][rightIndex].neighbors !== 0)
      break
  }
}

// const openAround = (minefield: Minefield[][], x: number, y: number) => {
// if (x > 0) {
//   minefield[x-1][y].isOpened = true
//   if (y > 0) {
//     minefield[x-1][y-1].isOpened = true
//     minefield[x][y-1].isOpened = true
//   }
//   if (y < 9) {
//     minefield[x-1][y+1].isOpened = true
//   }
//   if (x < 9) {
//   minefield[x+1][y].isOpened = true
//     if (y < 9) {
//       minefield[x+1][y+1].isOpened = true
//       minefield[x][y+1].isOpened = true
//     }
//     if (y > 0) {
//       minefield[x+1][y-1].isOpened = true
//     }
//   }
// }
// }

// const updateMinefield = (minefield: Minefield[][], x: number, y: number) => {
//   minefield[x][y].isOpened = true
//   if (minefield[x][y].isBomb) {
//     // game over
//   }
//   else if (minefield[x][y].neighbors === 0) { // those having neighbors are now also with empty message neighbors === 0
//     openAround(minefield, x, y)
//   }
// }

// const updateField = (minefield: Minefield[][]) => {
//   for(let indexX = 0; indexX < minefield.length; indexX++){
//     for(let indexY = 0; indexY < minefield[0].length; indexY++){
//       if (minefield[indexX][indexY].neighbors === 0 && !minefield[indexX][indexY].isBomb && minefield[indexX][indexY].isOpened){
//         openAround(minefield, indexX, indexY)
//       }
//     }
//   }
// }

const createMinefield = (rows: number, columns: number, numBombs: number) => {

  let _minefield: Minefield[][] = [];
  
  for (let i = 0; i < rows; i++) {
    _minefield[i] = _minefield[i] || [];
    for (let j = 0; j < rows; j++) {
      _minefield[i][j] = { isOpened: false, flagPlaced: false, isBomb: false, message: "", neighbors: 0, gameover: false }
    }
  }

  const bombs = [...Array(numBombs)].map(() => ([Math.floor(Math.random()*(rows-1)), Math.floor(Math.random()*(columns-1))]))
  console.log('bombs', bombs)

  bombs.forEach(([ x, y ]) => {
    _minefield[x][y] = { isOpened: false, flagPlaced: false, isBomb: true, message: "💣", neighbors: 0, gameover: false   };
    for(let i = Math.max(0, x-1); i <= Math.min(_minefield.length-1, x+1); i++) {
      for(let j = Math.max(0, y-1); j <= Math.min(_minefield[0].length-1, y+1); j++) {
        if (!_minefield[i][j].isBomb) {
          _minefield[i][j].neighbors = _minefield[i][j].neighbors + 1
        }
      }
    }
  })
  return _minefield
}

const updateField = (minefield: Minefield[][]) => {
  for(let indexX = 0; indexX < minefield.length; indexX++) {
    for(let indexY = 0; indexY < minefield[0].length; indexY++) {
      if (minefield[indexX][indexY].isOpened && minefield[indexX][indexY].neighbors === 0 && !minefield[indexX][indexY].isBomb) {
        updateMinefield(minefield, indexX, indexY)
      }
    }
  }
}

const tmp = createMinefield(DEFAULT_ROWS, DEFAULT_COLUMNS, DEFAULT_MINES);

const state = ref(tmp)
onUpdated(() => updateField(state.value))

</script>

<template>
  <header>
    <div @bing-neighbors="onBingNeighbors" class="wrapper">
      <div v-for="(n, numY) in DEFAULT_ROWS" class="gameRow">
        <Cell @bingNeighbors="onBingNeighbors" @toggleFlag="onToggleFlag" v-for="(m, numX) in DEFAULT_COLUMNS" :flagPlaced="state[numX][numY].flagPlaced" :gameover="state[numX][numY].gameover" :neighbors="state[numX][numY].neighbors" :isOpened=state[numX][numY].isOpened :isBomb=state[numX][numY].isBomb :message=state[numX][numY].message :x=numX :y=numY /> 
      </div>
    </div>
  </header>
</template>

<style>
@import './assets/base.css';

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

a,
.green {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}

@media (min-width: 1024px) {
  body {
    display: flex;
    place-items: center;
  }

  #app {
    display: grid;
    grid-template-columns: 1fr 1fr;
    padding: 0 2rem;
  }

  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
    line-height: 0;
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
