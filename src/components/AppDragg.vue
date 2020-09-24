<template>
  <div
    draggable
    @dragover.prevent
    @dragenter.prevent
    @dragstart.self="onDrag"
  >
    <slot/>
  </div>
</template>

<script>
export default {
  props: {
    transferData: {
      type: Object,
      required: true
    }
  },
  methods: {
    onDrag(e){
      e.dataTransfer.setData('payload', JSON.stringify(this.transferData))
    }
  }
}
</script>

<style scoped>

</style>
