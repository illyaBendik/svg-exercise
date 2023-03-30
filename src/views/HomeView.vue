<template>
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
    <template v-if="selectedSquare">
      <foreignObject :x="selectedSquare.x" :y="selectedSquare.y" width="100" height="100">
        <div class="popup">
          <!-- :style="{ left: selectedSquare.x + 10 + 'px', top: selectedSquare.y + 'px' }" -->
          Current color: {{ selectedSquare.color }}
        </div>
      </foreignObject>
    </template>
  </svg>
</template>

<script>
import { ref, watch } from 'vue'

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
      const numSquares = 100000
      // const numSquares = 1
      for (let i = 0; i < numSquares; i++) {
        squares.value.push({
          x: Math.floor(Math.random() * 995),
          y: Math.floor(Math.random() * 995),
          color: getRandomColor()
        })
      }
    }

    const updateSquareColors = () => {
      const numUpdates = 2000
      for (let i = 0; i < numUpdates; i++) {
        const index = Math.floor(Math.random() * squares.value.length)
        squares.value[index].color = getRandomColor()
        if (selectedSquare.value && index === selectedSquare.value.index) {
          selectedSquare.value.color = squares.value[index].color
        }
      }
    }

    const selectSquare = (event) => {
      const rect = event.target.closest('rect')
      if (rect) {
        const index = Number(rect.getAttribute('data-index'))
        selectedSquare.value = {
          index,
          x: squares.value[index].x + 5,
          y: squares.value[index].y - 20,
          color: squares.value[index].color
        }
      } else {
        selectedSquare.value = null
      }
    }

    initSquares()

    // Update square colors every 500ms
    setInterval(updateSquareColors, 1000)

    // Watch for changes to selectedSquare and update color accordingly
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
.popup {
  background-color: #fff;
  color: black;
  border: 1px solid #000;
  box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.3);
  padding: 5px;
  position: absolute;
  z-index: 1;
}
</style>
