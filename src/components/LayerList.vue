<template>
  <q-list separator bordered>
    <draggable class="dragArea" tag="div" :list="layers" :group="{ name: 'g1' }" @end="$emit('update')">
      <q-item clickable v-for="el in layers" :key="el.name" :active="el === activeLayer" @click="selectLayer(el)">
        <q-item-section avatar>
          <q-avatar rounded>
            <img :src="el.src">
          </q-avatar>
        </q-item-section>

        <q-item-section>{{ el.name }}</q-item-section>

        <q-item-section side>
          <q-btn icon="delete" round flat @click="deleteLayer(el)"></q-btn>
        </q-item-section>
      </q-item>
    </draggable>
    <q-item>
      <input type="file" @change="uploadNewLayer" accept = "image/*"/>
    </q-item>
  </q-list>
</template>
<script>
import draggable from 'vuedraggable'
export default {
  props: {
    layers: {
      required: true,
      type: Array
    }
  },
  components: {
    draggable
  },
  data: () => ({
    file: null,
    activeLayer: null
  }),
  methods: {
    uploadNewLayer (e) {
      let file = e.target.files[0]
      let fileReader = new FileReader()
      fileReader.readAsDataURL(file)
      let self = this
      fileReader.onloadend = () => {
        self.layers.push({
          name: file.name,
          src: fileReader.result,
          position: {
            x: 0,
            y: 0
          }
        })
        this.$emit('update')
      }
    },
    deleteLayer (layer) {
      let index = this.layers.indexOf(layer)
      if (index > -1) {
        this.layers.splice(index, 1)
      }
      this.$emit('update')
    },
    selectLayer (layer) {
      this.activeLayer = layer
      this.$emit('newActiveLayer', layer)
    }
  }
}
</script>
<style scoped>

</style>
