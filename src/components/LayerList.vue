<template>
  <q-list separator>
    <draggable class="dragArea" tag="div" :list="layers" :group="{ name: 'g1' }">
      <q-item v-for="el in layers" :key="el.name">
        <q-item-section avatar>
          <q-avatar>
            <img :src="el.image">
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
    file: null
  }),
  methods: {
    uploadNewLayer (e) {
      let file = e.target.files[0]
      let fileReader = new FileReader()
      fileReader.readAsDataURL(file)
      let self = this
      fileReader.onloadend = () => {
        self.layers.push({
          name: `Layer ${self.layers.length + 1}`,
          image: fileReader.result
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
    }
  }
}
</script>
<style scoped>

</style>
