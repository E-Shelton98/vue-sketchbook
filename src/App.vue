<template>
  <div id="app">
    <canvas
      ref="myCanvas"
      id="myCanvas"
      @mousemove="draw"
      @mousedown="beginDrawing"
      @mouseup="stopDrawing"
      @mouseout="stopDrawing"
    ></canvas>
    <DrawingToolbar @color-selection="setDrawingColor" @style-selection="setDrawingStyle"/>
  </div>
</template>

<script>
  import DrawingToolbar from './components/DrawingToolbar.vue'

  export default {
    name: 'App',
    components: {
      DrawingToolbar,
    },
    data: function () {
      return {
        canvas: null,
        x: 0,
        y: 0,
        isDrawing: false,
        color: 'rgb(0,0,0)',
        drawStyle: 'freehand',
      }
    },
    methods: {
      draw(e) {
        if (this.isDrawing) {
          if (this.drawStyle === 'freehand') {
            this.drawLine(this.x, this.y, e.offsetX, e.offsetY)
            this.x = e.offsetX
            this.y = e.offsetY
          }
        }
      },
      beginDrawing(e) {
        this.x = e.offsetX
        this.y = e.offsetY
        this.isDrawing = true
      },
      stopDrawing(e) {
        if (this.isDrawing) {
          if (this.drawStyle === 'freehand') {
            this.drawLine(this.x, this.y, e.offsetX, e.offsetY)
            this.x = 0
            this.y = 0
            this.isDrawing = false
          } else if (this.drawStyle === 'rectangle') {
            this.drawRect(this.x, this.y, e.offsetX, e.offsetY)
            this.isDrawing = false
          }
        }
      },
      drawLine(x1, y1, x2, y2) {
        let ctx = this.canvas
        ctx.beginPath()
        ctx.strokeStyle = this.color
        ctx.moveTo(x1, y1)
        ctx.lineTo(x2, y2)
        ctx.stroke()
        ctx.closePath()
      },
      drawRect(x1, y1, x2, y2) {
        let ctx = this.canvas
        ctx.beginPath()
        ctx.strokeStyle = this.color
        let length = x2 - x1
        let height = y2 - y1
        ctx.rect(x1, y1, length, height)
        ctx.stroke()
      },
      setDrawingColor(newColor) {
        this.color = newColor
      },
      setDrawingStyle(styleType) {
        this.drawStyle = styleType
      }
    },
    mounted() {
      let canvas = this.$refs['myCanvas']
      canvas.width = window.innerWidth * 0.85
      canvas.height = window.innerHeight * 0.98
      this.canvas = canvas.getContext('2d')
    },
  }
</script>

<style>
  * {
    margin: 0px;
    padding: 0px;
    box-sizing: border-box;
  }
  #app {
    display: flex;
    height: 100vh;
    width: 100%;
  }
  #myCanvas {
    border: 1px solid grey;
    margin: 5px 0px 5px 5px;
    height: 98vh;
  }
</style>