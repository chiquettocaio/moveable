<template>
  <div class="player">
    <Keyframe
      v-for="(kf, index) of keyframes"
      :active="activeKfIndex === index"
      :key="`kf-${index}`"
      @click.native="keyframeClicked(kf, index)"/>
  </div>
</template>

<script>
export default {
  props: {
    keyframes: {
      type: Array,
      required: true,
      default: () => []
    },

    animationTime: {
      type: Number,
      required: true,
      default: 0
    },

    animationInstance: {
      type: [Object, Animation],
      required: true
    }
  },

  data: () => ({
    activeKfIndex: null
  }),

  methods: {
    keyframeClicked (kf, index) {
      this.activeKfIndex = index
      console.log(this.animationTime * kf.offset)
      const kfAnimationTime = this.animationTime * kf.offset
      this.animationInstance.currentTime = kfAnimationTime
    }
  }
}
</script>

<style scoped>
  .player {
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: space-evenly;
    bottom: 50px;
    width: 500px;
    height: 50px;
    background: rgb(54, 46, 122);
    border-radius: 4px;
  }

  .player > .kf {
    width: 20px;
    height: 20px;
    background: rgb(201, 201, 201);
    border-radius: 2px;
    cursor: pointer;
  }
</style>