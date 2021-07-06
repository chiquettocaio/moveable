  <template>
  <div class="bg">
    <div class="wrapper">
      <div
        v-for="(element, index) of elements"
        :data-index="index"
        :key="element"
        :class="element"
        ref="block"
        class="block"
        @click="elementClicked"/>
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
  </div>
</template>

<script>
import Moveable from 'vue-moveable';

export default {
  name: 'Canvas',

  components: {
    Moveable,
  },

  data: () => ({
    animationRunning: true,
    animationState: 'Pause',
    animationInstances: [],

    elements: ['st', 'nd', 'rd', 'th'],

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

      for (const anim of this.animationInstances) {
        anim[this.animationRunning ? 'pause' : 'play']()
      }

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
      /* Animation contains "transform", so every "transform" that Moveable tries to apply will fail */
      // const keyframes = [
      //   [
      //     { transform: 'scale(1)' },
      //     { transform: 'scale(.3)' },
      //   ], [
      //     { transform: 'translateY(0) translateX(0)' },
      //     { transform: 'translateY(-100px) translateX(0)' },
      //     { transform: 'translateY(-100px) translateX(100px)' },
      //     { transform: 'translateY(0) translateX(100px)' },
      //     { transform: 'translateY(0) translateX(0)' },
      //   ], [
      //     { transform: 'translateY(0) translateX(0)' },
      //     { transform: 'translateY(100px) translateX(-100px)' },
      //     { transform: 'translateY(100px) translateX(0)' },
      //     { transform: 'translateY(0) translateX(0)' }
      //   ], [
      //     { transform: 'rotateY(0)' },
      //     { transform: 'rotateY(360deg)' }
      //   ]
      // ]

      /* Animation contains "top, right, left, bottom", so every "top, right,
      left, bottom" that Moveable tries to apply will fail */
      const keyframes = [
        [
          { top: '-100px' },
          { top: '0px' },
        ], [
          { right: '-100px' },
          { right: '0px' }
        ], [
          { left: '-100px' },
          { left: '0px' },
        ], [
          { bottom: '-100px' },
          { bottom: '0px' }
        ]
      ]

      const options = {
        duration: 1000,
        iterations: 'Infinity',
        direction: 'alternate',
        easing: 'ease-in-out'
      }

      const blocks = this.$refs.block
      for (let i = 0; i < blocks.length; i++) {
        const animation = blocks[i].animate(keyframes[i], options)
        this.animationInstances.push(animation)
      }
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
    width: 160px;
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

  .nd {
    background: rgb(243, 134, 134);
    right: 0;
  }

  .rd {
    background: rgb(140, 238, 140);
    bottom: 0;
  }

  .th {
    background: rgb(146, 148, 253);
    bottom: 0;
    right: 0;
  }
</style>
