<template>
  <div id="app">
    <svg xmlns="http://www.w3.org/2000/svg" width="300" height="300">
      <Tweezing :to="rectangleA" tween="tweezer" :duration="200">
        <rect slot-scope="rectangle" 
          :x="rectangle.x" 
          :y="rectangle.y" 
          :width="rectangle.width" 
          :height="rectangle.height" 
        />
      </Tweezing>
      
      <Tweezing :to="rectangleB" tween="tweezer" :duration="300">
        <rect slot-scope="rectangle" 
          :x="rectangle.x" 
          :y="rectangle.y" 
          :width="rectangle.width" 
          :height="rectangle.height" 
        />
      </Tweezing>
    </svg>
    <br>
    <button @click="redraw">
      redraw
    </button>
    overlap: {{ overlap }} <span v-if="overlap">(area: {{ intersectX }} &times; {{ intersectY }})</span><br>
  </div>
</template>

<script>
import Vue from 'vue'
import { Tweezing, tweezerHelper } from 'vue-tweezing'
// import Tweezer to use it as our Tweening engine
import Tweezer from 'tweezer.js'

// install the plugin with one single engine
// use the tweezerHelper to generate the function
// needed by VueTweezing to handle tweezing
Vue.use(Tweezing, {
  tweezer: tweezerHelper(Tweezer)
})

const getRandomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min) + min)
}
export default {
  name: 'App',
  data() {
    return {
      a: {
        min: { x: 50, y: 50 },
        max: { x: 150, y: 150 }
      },
      b: {
        min: { x: 100, y: 100 },
        max: { x: 200, y: 200 }
      }
    }
  },
  methods: {
    redraw() {
      this.a.min.x = getRandomNumber(1, 300)
      this.a.min.y = getRandomNumber(1, 300)
      this.a.max.x = getRandomNumber(this.a.min.x + 1, 300)
      this.a.max.y = getRandomNumber(this.a.min.y + 1, 300)

      this.b.min.x = getRandomNumber(1, 300)
      this.b.min.y = getRandomNumber(1, 300)
      this.b.max.x = getRandomNumber(this.b.min.x + 1, 300)
      this.b.max.y = getRandomNumber(this.b.min.y + 1, 300)
    }
  },
  computed: {
    rectangleA() {
      return {
        x: this.a.min.x,
        y: this.a.min.y,
        width: this.dimensions.a.width,
        height: this.dimensions.a.height
      }
    },
    rectangleB() {
      return {
        x: this.b.min.x,
        y: this.b.min.y,
        width: this.dimensions.b.width,
        height: this.dimensions.b.height
      }
    },
    dimensions() {
      return {
        a: {
          width: this.a.max.x - this.a.min.x,
          height: this.a.max.y - this.a.min.y
        },
        b: {
          width: this.b.max.x - this.b.min.x,
          height: this.b.max.y - this.b.min.y
        }
      }
    },
    boundaries() {
      return {
        x: {
          min: Math.min(this.a.min.x, this.b.min.x),
          max: Math.max(this.a.max.x, this.b.max.x)
        },
        y: {
          min: Math.min(this.a.min.y, this.b.min.y),
          max: Math.max(this.a.max.y, this.b.max.y)
        }
      }
    },
    intersectX() {
      return (
        this.dimensions.a.width +
        this.dimensions.b.width -
        (this.boundaries.x.max - this.boundaries.x.min)
      )
    },
    intersectY() {
      return (
        this.dimensions.a.height +
        this.dimensions.b.height -
        (this.boundaries.y.max - this.boundaries.y.min)
      )
    },
    overlap() {
      return this.intersectX > 0 && this.intersectY > 0
    }
  }
}
</script>

<style>
svg {
  background: #f7f7f7;
}
svg > rect:nth-of-type(1) {
  fill: #fa8072;
  opacity: 0.5;
}
svg > rect:nth-of-type(2) {
  fill: #40e0d0;
  opacity: 0.5;
}
</style>
