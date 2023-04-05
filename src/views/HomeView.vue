<template>
  <div class="wrapper">
    <svg ref="svg" :width="width" :height="height">
      <rect
        v-for="(square, index) in squares"
        :key="index"
        :x="square.x"
        :y="square.y"
        @click="selectSquare({ square, index })"
        :width="SQUARE_SIZE"
        :height="SQUARE_SIZE"
        :fill="square.color"
        :class="{ selected: selectedSquare && selectedSquare.index === index }"
      />
    </svg>
    <div
      v-if="selectedSquare"
      :style="{ left: selectedSquare.x + 'px', top: selectedSquare.y + 'px' }"
      class="popup"
    >
      Current color: {{ selectedSquare.color }}
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'

const AMOUNT_SQUARES = 10000
const SQUARE_SIZE = 30
const LIMIT_UPDATE = 2000

export default {
  setup() {
    const width = window.innerWidth
    const numCols = Math.floor(width / SQUARE_SIZE)

    const selectedSquare = ref(null)
    const rows = ref(0)
    const height = computed(() => (rows.value === 0 ? SQUARE_SIZE : rows.value * SQUARE_SIZE))

    const getRandomColor = () => {
      const r = Math.floor(Math.random() * 256)
      const g = Math.floor(Math.random() * 256)
      const b = Math.floor(Math.random() * 256)
      return `rgb(${r}, ${g}, ${b})`
    }

    const createSquares = () => {
      const squares = []
      for (let i = 0; i < AMOUNT_SQUARES; i++) {
        const row = Math.floor(i / numCols)
        if (rows.value !== row) {
          rows.value = row
        }
        const col = i % numCols
        squares.push({
          x: col * SQUARE_SIZE,
          y: row * SQUARE_SIZE,
          color: getRandomColor()
        })
      }
      return squares
    }

    const updateSquareColors = () => {
      for (let i = 0; i < LIMIT_UPDATE; i++) {
        const index = Math.floor(Math.random() * squares.value.length)
        squares.value[index].color = getRandomColor()
        if (selectedSquare.value && index === selectedSquare.value.index) {
          selectedSquare.value.color = squares.value[index].color
        }
      }
    }

    const selectSquare = ({ square, index }) => {
      selectedSquare.value = {
        index,
        x: square.x + 15,
        y: square.y + 15,
        color: squares.value[index].color
      }
    }

    const squares = ref(createSquares())

    const update = () => {
      updateSquareColors()
      requestAnimationFrame(update)
    }

    onMounted(() => {
      requestAnimationFrame(update)
    })

    return { squares, width, height, SQUARE_SIZE, selectSquare, selectedSquare }
  }
}
</script>
<style>
.wrapper {
  overflow-y: scroll;
  height: 100vh;
}

.popup {
  width: 100px;
  height: 100px;
  background-color: #fff;
  color: black;
  border: 1px solid #000;
  box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.3);
  padding: 5px;
  position: absolute;
  z-index: 1;
}
.selected {
  stroke: black;
  stroke-width: 5;
}
</style>
