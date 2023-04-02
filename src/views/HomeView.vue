<template>
  <div class="wrapper">
    <svg width="1000" height="1000" @click="selectSquare">
      <rect
        v-for="(square, index) in squares"
        :key="index"
        :x="square.x"
        :y="square.y"
        width="5"
        height="5"
        :fill="square.color"
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
import { ref, watch } from 'vue'
const MAX_COUNT = 100000
const LIMIT = 10000

export default {
  setup() {
    const squares = ref([])
    const selectedSquare = ref(null)

    const getRandomColor = () => {
      const r = Math.floor(Math.random() * 256)
      const g = Math.floor(Math.random() * 256)
      const b = Math.floor(Math.random() * 256)
      return `rgb(${r}, ${g}, ${b})`
    }

    const initSquares = () => {
      const numSquares = MAX_COUNT
      for (let i = 0; i < numSquares; i++) {
        squares.value.push({
          x: Math.floor(Math.random() * 995),
          y: Math.floor(Math.random() * 995),
          color: getRandomColor()
        })
      }
    }

    const updateSquareColors = () => {
      const numUpdates = LIMIT
      for (let i = 0; i < numUpdates; i++) {
        const index = Math.floor(Math.random() * squares.value.length)
        squares.value[index].color = getRandomColor()
        if (selectedSquare.value && index === selectedSquare.value.index) {
          selectedSquare.value.color = squares.value[index].color
        }
      }
    }

    const selectSquare = (event) => {
      selectedSquare.value = null
      const rect = event.target.closest('rect')
      if (rect) {
        const index = Number(rect.getAttribute('data-index'))
        selectedSquare.value = {
          index,
          x: event.clientX,
          y: event.clientY,
          color: squares.value[index].color
        }
      }
    }

    initSquares()

    setInterval(updateSquareColors, 1000)

    watch(selectedSquare, (newVal) => {
      if (newVal) {
        newVal.color = squares.value[newVal.index].color
      }
    })

    return {
      squares,
      selectedSquare,
      selectSquare
    }
  }
}
</script>

<style>
.wrapper {
  display: flex;
  justify-content: center;
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
</style>
