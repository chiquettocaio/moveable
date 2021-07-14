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

    <button @click="toggleAnimationState()"> 
      <img
        :src="imageData.src"
        :alt="imageData.alt" />
    </button>

    <KeyframesBar
      v-if="!animationRunning"
      :keyframes="this.elementKfs"
      :animationTime="animationTime"
      :animationInstance="elementAnimation"
      :activeKeyframe="this.currentKfIndex"
      @keyframeClicked="updateCurrentKeyframe"
    />
  </div>
</template>

<script>
import Moveable from 'vue-moveable'
import PlayImage from '../../assets/play.png'
import PauseImage from '../../assets/pause.png'

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
    currentKfIndex: null,
    elementKfs: [
      { left: '0px', top: '0px', offset: 0 },
      { left: '425px', top: '0px', offset: 0.25 },
      { left: '425px', top: '425px', offset: 0.5 },
      { left: '0px', top: '425px', offset: 0.75 },
      { left: '0px', top: '0px', offset: 0.999 },
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

  computed: {
    imageData () {
      return {
        src: this.animationRunning ? PauseImage : PlayImage,
        alt: this.animationRunning ? 'Pause' : 'Play',
      }
    }
  },

   methods: {
    toggleAnimationState() {
      this.resetMoveableTarget()
      this.animationState = +this.animationRunning
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

    updateCurrentKeyframe (index) {
      this.currentKfIndex = index
    },

    createAnimation () {
      const options = {
        duration: this.animationTime,
        iterations: 'Infinity',
        easing: 'linear'
      }

      const animatedElement = this.$refs.block
      const animation = animatedElement.animate(this.elementKfs, options)
      this.elementAnimation = animation
    },

    handleDrag({ left, top }) {
      if (+this.currentKfIndex >= 0) {
        const animationKfs = this.elementAnimation.effect.getKeyframes()
        animationKfs[this.currentKfIndex].top = `${top}px`
        animationKfs[this.currentKfIndex].left = `${left}px`
        this.elementAnimation.effect.setKeyframes(animationKfs)
      }
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
    width: 90px;
    height: 90px;
    padding-top: 8px;
    background: #CECECE;
    border: none;
    border-radius: 4px;
    font-size: 30px;
    cursor: pointer;
  }

  .wrapper {
    position: relative;
    width: 500px;
    height: 500px;
    background: #5847b9;
    border-radius: 4px;
    overflow: hidden;
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
