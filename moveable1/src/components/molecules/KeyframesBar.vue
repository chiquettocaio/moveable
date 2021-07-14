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
    },

    activeKeyframe: {
      type: Number,
      required: false,
      default: 0
    }
  },

  data: () => ({
    activeKfIndex: null
  }),

  methods: {
    keyframeClicked (kf, index) {
      this.activeKfIndex = index
      const kfAnimationTime = this.animationTime * kf.offset
      this.animationInstance.currentTime = kfAnimationTime
      this.$emit('keyframeClicked', index)
    }
  },

  mounted () {
    this.activeKfIndex = this.activeKeyframe
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
    background: #5847b9;
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