<template>
  <div
    class="keyframe"
    :class="active ? 'active' : ''"/>
</template>

<script>
export default {
  props: {
    active: {
      type: Boolean,
      required: false,
      default: false
    }
  }
}
</script>

<style scoped>
  .keyframe {
    width: 20px;
    height: 20px;
    background: rgb(219, 219, 219);
    border-radius: 50%;
    cursor: pointer;
  }

  .keyframe.active {
    background: rgb(240, 236, 35);
  }
</style>