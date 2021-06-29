<template>
  <div class="bg">
    <div class="wrapper">
      <div
        v-for="element of elements"
        :key="element"
        :class="element"
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
    animationState: 'Pause',

    elements: ['st', 'nd', 'rd', 'th'],

    showMoveableGuides: false,
    lastTarget: null,

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

      const wrapper = document.querySelector('.wrapper')
      const playState = window.getComputedStyle(wrapper).animationPlayState
      const isRunning = /running/.test(playState)

      wrapper.style.animationPlayState = isRunning ? 'paused' : 'running'      
      this.animationState = isRunning ? 'Run' : 'Pause'
    },

    elementClicked ({ target }) {
      this.resetMoveableTarget()
      this.moveable.target = target
      this.showMoveableGuides = true
    },

    resetMoveableTarget () {
      this.showMoveableGuides = false
      this.lastTarget = this.moveable.target
      this.moveable.target = null
    },

    handleDrag({ target, transform }) {
      const wrapper = document.querySelector('.wrapper')
      wrapper.style.animationPlayState = 'paused'

      target.style.transform = transform;
    },

    handleResize({ target, width, height, delta }) {
      console.log('onResize', width, height);
      delta[0] && (target.style.width = `${width}px`);
      delta[1] && (target.style.height = `${height}px`);
    },

    handleScale({ target, transform, scale }) {
      console.log('onScale scale', scale);
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
    },
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
    width: 160px;
    animation: sillyAnimation 1s ease-in-out 0s infinite alternate-reverse;
  }

  .block {
    display: inline-block;
    border-radius: 50%;
    width: 75px;
    height: 75px;
  }

  .st {
    background: rgb(219, 135, 252);
    margin: 0 10px 10px 0;
  }

  .nd {
    background: rgb(243, 134, 134);
    margin: 0 0 10px 0;
  }

  .rd {
    background: rgb(140, 238, 140);
    margin: 0 10px 0 0;
  }

  .th {
    background: rgb(146, 148, 253);
  }

  @keyframes sillyAnimation {
    0% { transform: rotateZ(0deg) scale(1); }

    100% { transform: rotateZ(360deg) scale(.5); }
  }
</style>
