<template>
  <q-page class="flex flex-center">
    <a href="#" id="downloader" @click="download" download="image.png">Download!</a>
    <canvas id="canvas" class="preview" @mousedown="startDragging" @mouseup="stopDragging" @mousemove="move" @wheel="scrolling"></canvas>
    <layer-list :layers="layers" @update="draw()" @newActiveLayer="onNewActiveLayer"></layer-list>
  </q-page>
</template>

<script>
import LayerList from '../components/LayerList'
export default {
  name: 'PageIndex',
  components: { LayerList },
  data: () => ({
    canvas: null,
    layers: [],
    activeLayer: null,
    dragging: false,
    offsetToImage: null,
    interval: null
  }),
  methods: {
    download () {
      document.getElementById('downloader').download = 'image.png'
      document.getElementById('downloader').href = document.getElementById('canvas').toDataURL('image/png').replace(/^data:image\/[^;]/, 'data:application/octet-stream')
    },
    draw () {
      console.log('draw')
      let ctx = this.canvas.getContext('2d')
      ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)
      this.layers.forEach(layer => {
        if (layer.img == null) {
          let img = new Image()
          img.src = layer.src
          layer.img = img
          layer.width = img.width
          layer.height = img.height
          img.onload = function () {
            ctx.drawImage(img, layer.position.x, layer.position.y, layer.width, layer.height)
          }
        } else {
          console.log(layer.img.width)
          ctx.drawImage(layer.img, layer.position.x, layer.position.y, layer.width, layer.height)
        }
      })
      setTimeout(function () {
      }, 1000)
    },
    startDragging (e) {
      let layer = this.activeLayer
      let pos = this.activeLayer.position
      if (e.offsetX >= pos.x && e.offsetX <= pos.x + layer.width &&
        e.offsetY >= pos.y && e.offsetY <= pos.y + layer.height) {
        this.dragging = true
        this.offsetToImage = { x: e.offsetX - pos.x, y: e.offsetY - pos.y }
      }
    },
    stopDragging () {
      this.dragging = false
      clearInterval(this.interval)
      this.interval = null
    },
    move (e) {
      if (this.dragging) {
        if (this.interval == null) {
          this.interval = window.setInterval(this.draw, 100)
        }
        this.layers.forEach(layer => {
          if (layer === this.activeLayer) {
            layer.position.x = e.offsetX - this.offsetToImage.x
            layer.position.y = e.offsetY - this.offsetToImage.y
          }
        })
      }
    },
    scrolling (event) {
      let delta = Math.sign(event.deltaY) * 0.1
      this.layers.forEach(layer => {
        if (layer === this.activeLayer) {
          console.log('update', layer.img.width * (1 + delta))
          layer.width = layer.width * (1 + delta)
          layer.height = layer.height * (1 + delta)
        }
      })
      this.draw()
      event.preventDefault()
    },
    onNewActiveLayer (layer) {
      this.activeLayer = layer
    }
  },
  mounted () {
    this.canvas = document.getElementById('canvas')
    this.canvas.width = 400
    this.canvas.height = 400
    this.draw()
  }
}
</script>

<style>
  .preview {
    height: 400px;
    width: 400px;
    border: 1px black solid;
  }
</style>
