  <template>
  <div class="bg">
    <div class="wrapper">
      <div
        ref="block"
        class="block st"
        @click="elementClicked"
      />
    </div>

    <Moveable
      v-show="showMoveableGuides"
      ref="moveable"
      class="moveable"
      v-bind="moveable"
      @drag="handleDrag"
      @resize="handleResize"
      @scale="handleScale"
      @rotate="handleRotate"
      @warp="handleWarp" />

    <button @click="toggleAnimationState()"> {{ animationState }} </button>

    <KeyframesBar
      v-if="!animationRunning"
      :keyframes="this.elementKfs"
      :animationTime="animationTime"
      :animationInstance="elementAnimation"
    />
  </div>
</template>

<script>
import Moveable from 'vue-moveable'

export default {
  name: 'Canvas',

  components: {
    Moveable
  },

  data: () => ({
    animationRunning: true,
    animationState: 'Pause',
    animationInstances: [],

    elementAnimation: null,
    animationTime: 4000,
    elementKfs: [
      { left: '0px', top: '0px', background: 'red', offset: 0 },
      { left: '425px', top: '0px', background: 'blue', offset: 0.25 },
      { left: '425px', top: '200px', background: 'pink', offset: 0.5 },
      { left: '0px', top: '200px', background: 'tomato', offset: 0.75 },
      { left: '0px', top: '0px', background: 'white', offset: 1 },
    ],

    showMoveableGuides: false,

    moveable: {
      draggable: true,
      throttleDrag: 1,
      resizable: false,
      throttleResize: 1,
      keepRatio: false,
      scalable: true,
      throttleScale: 0.01,
      rotatable: true,
      throttleRotate: 0.2,
      pinchable: true,
      origin: false,
    },
  }),

   methods: {
    toggleAnimationState() {
      this.resetMoveableTarget()
      this.animationState = this.animationRunning ? 'Play' : 'Pause'
      this.elementAnimation[this.animationRunning ? 'pause' : 'play']()
      this.animationRunning = !this.animationRunning
    },

    elementClicked ({ target }) {
      if (!this.animationRunning) {
        this.resetMoveableTarget()
        this.moveable.target = target
        this.showMoveableGuides = true
      }
    },

    resetMoveableTarget () {
      this.showMoveableGuides = false
      this.moveable.target = null
    },

    createAnimation () {
      const options = {
        duration: this.animationTime,
        // iterations: 'Infinity',
        easing: 'linear'
      }

      const animatedElement = this.$refs.block
      const animation = animatedElement.animate(this.elementKfs, options)
      this.elementAnimation = animation
      console.log(animation)
    },

    handleDrag({ target, transform }) {
      target.style.transform = transform;
    },

    handleResize({ target, width, height, delta }) {
      console.log('onResize', width, height);
      delta[0] && (target.style.width = `${width}px`);
      delta[1] && (target.style.height = `${height}px`);
    },

    handleScale({ target, transform}) {
      console.log('onScale scale', `${transform}!important`);
      target.style.transform = transform;
    },

    handleRotate({ target, dist, transform }) {
      console.log('onRotate', dist);
      target.style.transform = transform;
    },

    handleWarp({ target, transform }) {
      console.log('onWarp', transform);
      target.style.transform = transform;
    },

    handlePinch({ target }) {
      console.log('onPinch', target);
    }
  },

  mounted () {
    this.createAnimation()
  }
}
</script>

<style scoped>
  .bg {
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    background: #000;
  }

  button {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 100px;
    height: 50px;
    background: #CECECE;
    border: none;
    border-radius: 4px;
    font-size: 30px;
    cursor: pointer;
  }

  .wrapper {
    position: relative;
    width: 500px;
    height: 160px;
  }

  .block {
    position: absolute;
    border-radius: 50%;
    width: 75px;
    height: 75px;
  }

  .st {
    background: rgb(219, 135, 252);
  }
</style>
