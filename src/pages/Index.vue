<template>
  <q-page class="flex flex-center">
    <a href="#" id="downloader" @click="download" download="image.png">Download!</a>
    <canvas id="canvas" class="preview"></canvas>
    <layer-list :layers="layers" @update="draw()"></layer-list>
  </q-page>
</template>

<script>
import LayerList from '../components/LayerList'
export default {
  name: 'PageIndex',
  components: { LayerList },
  data: () => ({
    canvas: null,
    layers: []
  }),
  methods: {
    download () {
      document.getElementById('downloader').download = 'image.png'
      document.getElementById('downloader').href = document.getElementById('canvas').toDataURL('image/png').replace(/^data:image\/[^;]/, 'data:application/octet-stream')
    },
    draw () {
      console.log('draw')
      console.log(this.layers)
      let canvas = document.getElementById('canvas')
      canvas.width = 200
      canvas.height = 200
      let ctx = canvas.getContext('2d')
      this.layers.forEach(layer => {
        console.log('test')
        let img = new Image()
        img.src = layer.image
        img.onload = function () {
          ctx.drawImage(img, 0, 0)
        }
      })
    }
  },
  mounted () {
    this.canvas = document.getElementById('canvas')
    this.canvas.onmousedown = (event) => {
      console.log(event.offsetX)
    }
    this.canvas.width = 400
    this.canvas.height = 400
    this.draw()
  }
}
</script>

<style>
  .preview {
    height: 200px;
    width: 200px;
    border: 1px black solid;
  }
</style>
