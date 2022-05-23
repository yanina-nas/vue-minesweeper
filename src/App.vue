<script setup lang="ts">
import Cell from './components/Cell.vue'
// import Game from './components/Game.vue'
import { ref } from 'vue'

const DEFAULT_ROWS = 10
const DEFAULT_COLUMNS = 10
const DEFAULT_MINES = 10

interface Minefield {
  isOpened: boolean;
  isBomb: boolean;
  message: string;
  neighbors: number;
}

const onBingNeighbors = (x: number, y: number) => {
  updateMinefield(state.value, x, y)
}

const openAround = (minefield: Minefield[][], x: number, y: number) => {
  let min_i = Math.max(0, x-1)
  let max_i = Math.min(minefield.length-1, x+1)
  let min_j = Math.max(0, y-1)
  let max_j = Math.min(minefield[0].length-1, y+1)

  for(let i = min_i; i <= max_i; i++) {
    for(let j = min_j; j <= max_j; j++) {
      minefield[i][j].isOpened = true
    }
  }
}

const updateMinefield = (minefield: Minefield[][], x: number, y: number) => {
  minefield[x][y].isOpened = true
  if (minefield[x][y].isBomb) {
    // game over
  }
  else if (minefield[x][y].neighbors === 0) { // those having neighbors are now also with empty message neighbors === 0
    openAround(minefield, x, y)
  }
}

const createMinefield = (rows: number, columns: number, numBombs: number) => {

  let _minefield: Minefield[][] = [];
  
  for (let i = 0; i < rows; i++) {
    _minefield[i] = _minefield[i] || [];
    for (let j = 0; j < rows; j++) {
      _minefield[i][j] = { isOpened: false, isBomb: false, message: "", neighbors: 0 }
    }
  }

  const bombs = [...Array(numBombs)].map(() => ([Math.floor(Math.random()*(rows-1)), Math.floor(Math.random()*(columns-1))]))
  console.log('bombs', bombs)

  bombs.forEach(([ x, y ]) => {
    _minefield[x][y] = { isOpened: false, isBomb: true, message: "💣", neighbors: 0  };
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

const tmp = createMinefield(DEFAULT_ROWS, DEFAULT_COLUMNS, DEFAULT_MINES);

const state = ref(tmp)

</script>

<template>
  <header>
    <!-- <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" /> -->
    <div @bing-neighbors="onBingNeighbors" class="wrapper">
      <div v-for="(n, numY) in DEFAULT_ROWS" class="gameRow">
        <Cell @bingNeighbors="onBingNeighbors" v-for="(m, numX) in DEFAULT_COLUMNS" :neighbors="state[numX][numY].neighbors" :isOpened=state[numX][numY].isOpened :isBomb=state[numX][numY].isBomb :message=state[numX][numY].message :x=numX :y=numY /> 
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
