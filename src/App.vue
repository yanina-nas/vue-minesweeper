<script setup lang="ts">
import Cell from './components/Cell.vue'
// import Game from './components/Game.vue'
import { reactive, ref } from 'vue'

const DEFAULT_ROWS = 10
const DEFAULT_COLUMNS = 10
const DEFAULT_MINES = 10

interface Minefield {
  open: boolean;
  isBomb: boolean;
  message: string;
}

const createMinefield = (rows: number, columns: number, numBombs: number) => {

  let _minefield: Minefield[][] = [];
  
  for (let i = 0; i < rows; i++) {
    _minefield[i] = _minefield[i] || [];
    for (let j = 0; j < rows; j++) {
      _minefield[i][j] = { open: false, isBomb: false, message: "" }
    }
  }

  console.log('minefield', _minefield)

  const bombs = [...Array(numBombs)].map(() => ([Math.floor(Math.random()*(rows-1)), Math.floor(Math.random()*(columns-1))]))
  console.log('bombs', bombs)

  console.log(_minefield);

  bombs.forEach(([ x, y ]) => {
    console.log([ x, y ]);
    _minefield[x][y] = { open: false, isBomb: true, message: "💣" };
  })

  console.log('minefieldWithBomb', _minefield)

  return _minefield
}

const tmp = createMinefield(DEFAULT_ROWS, DEFAULT_COLUMNS, DEFAULT_MINES);

const state = ref(tmp)

function bingNeighbors(x: number, y: number) {
  console.log('bing my neighbours! I a cell am at ', x, ' ', y)
  console.log("I work")
  // update state => this one and those around auto-open
}

</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
    <div @bing-neighbors="bingNeighbors" class="wrapper">
      <div v-for="(n, numY) in DEFAULT_ROWS" class="gameRow">
        <Cell v-for="(m, numX) in DEFAULT_COLUMNS" :isBomb=state[numX][numY].isBomb :message=state[numX][numY].message :x=numX :y=numY /> 
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
