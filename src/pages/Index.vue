<template>
  <q-page class="flex flex-center">
    <a href="#" id="downloader" @click="download" download="image.png">Download!</a>
    <canvas id="canvas" class="preview"></canvas>
    <div>
      <q-input v-model="body" label="Body" rounded>
        <template v-slot:prepend>
          <q-btn round dense flat icon="remove" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="add" />
        </template>
      </q-input>
      <q-input v-model="eye" label="Eye" rounded>
        <template v-slot:prepend>
          <q-btn round dense flat icon="remove" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="add" />
        </template>
      </q-input>
      x: {{ eyeX }}
      <q-slider v-model="eyeX" :min="0" :max="200"  @change="draw"/>
      y: {{ eyeY }}
      <q-slider v-model="eyeY" :min="0" :max="200" color="green" @change="draw"/>
      Scale: {{ eyeScale }}
      <q-slider v-model="eyeScale" :min="0" :max="2" :step="0.1" @change="draw"/>
      <q-input v-model="mouth" label="Mouth" rounded >
        <template v-slot:prepend>
          <q-btn round dense flat icon="remove" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="add" />
        </template>
      </q-input>
      x: {{ mouthX }}
      <q-slider v-model="mouthX" :min="0" :max="200" @change="draw"/>
      y: {{ mouthY }}
      <q-slider v-model="mouthY" :min="0" :max="200" color="green" @change="draw"/>
      Scale: {{ mouthScale }}
      <q-slider v-model="mouthScale" :min="0" :max="2" :step="0.1" @change="draw"/>
    </div>
  </q-page>
</template>

<script>
import assets from '../statics/assets/assets.json'
export default {
  name: 'PageIndex',
  data: () => ({
    body: 1,
    eye: 1,
    eyeX: 0,
    eyeY: 0,
    eyeScale: 1,
    mouth: 1,
    mouthX: 0,
    mouthY: 0,
    mouthScale: 1
  }),
  methods: {
    download () {
      document.getElementById('downloader').download = 'image.png'
      document.getElementById('downloader').href = document.getElementById('canvas').toDataURL('image/png').replace(/^data:image\/[^;]/, 'data:application/octet-stream')
    },
    draw () {
      console.log('draw')
      let canvas = document.getElementById('canvas')
      canvas.width = 200
      canvas.height = 200
      let ctx = canvas.getContext('2d')
      let bodyImage = new Image()
      bodyImage.src = assets.bodies[this.body - 1]
      bodyImage.onload = function (ev) {
        ctx.drawImage(bodyImage, 0, 0)
      }
      let mouthImage = new Image()
      mouthImage.src = assets.mouths[this.mouth - 1]

      mouthImage.onload = () => {
        ctx.drawImage(mouthImage, this.mouthX, this.mouthY, mouthImage.width * this.mouthScale, mouthImage.height * this.mouthScale)
      }

      let eyeImage = new Image()
      eyeImage.src = assets.eyes[this.eye - 1]

      eyeImage.onload = () => {
        ctx.drawImage(eyeImage, this.eyeX, this.eyeY, eyeImage.width * this.eyeScale, eyeImage.height * this.eyeScale)
      }
    }
  },
  mounted () {
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
