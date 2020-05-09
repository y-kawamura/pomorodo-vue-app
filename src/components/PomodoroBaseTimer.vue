<template>
  <div clas="pomodoro-timer">
    <div class="pomodoro-timer--time-group">
      <span class="pomodoro-timer--time"
        >{{ workTimerMin }} : {{ workTimerSec }}</span
      >
      <svg
        class="pomodoro-timer--circle"
        width="300"
        height="300"
        xmlns="http://www.w3.org/2000/svg"
      >
        <g>
          <circle
            id="circle"
            class="circle-animation"
            r="144"
            cy="150"
            cx="150"
            stroke-width="8"
            fill="none"
            :stroke-dasharray="circleLength"
            :stroke-dashoffset="circleLength - restCircle"
            :stroke="circleColor"
          />
        </g>
      </svg>
    </div>
    <div class="pomodoro-timer--button-group">
      <!-- start -->
      <button
        v-show="!isWorking || (isWorking && isPause)"
        @click="startTimer"
        class="pomodoro-timer--button pomodoro-timer--button-orange"
      >
        Start
      </button>
      <!-- pause -->
      <button
        v-show="isWorking && !isPause"
        @click="pauseTimer"
        class="pomodoro-timer--button pomodoro-timer--button-orange"
      >
        Pause
      </button>

      <!-- stop -->
      <button
        v-show="!skip"
        @click="cancelTimer"
        class="pomodoro-timer--button pomodoro-timer--button-gray"
      >
        Cancel
      </button>
      <button
        v-show="skip"
        @click="skipTimer"
        class="pomodoro-timer--button pomodoro-timer--button-gray"
      >
        Skip
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    settingTime: {
      type: Number,
      required: true,
    },
    autoStart: {
      type: Boolean,
      default: false,
    },
    skip: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      workTimer: null,
      workTime: this.settingTime,
      startTime: null,
      isPause: false,
    }
  },
  computed: {
    workTimerMin() {
      return ('00' + Math.floor(this.workTime / 60)).slice(-2)
    },
    workTimerSec() {
      return ('00' + (this.workTime % 60)).slice(-2)
    },
    isWorking() {
      return this.workTimer
    },
    circleLength() {
      return 2 * 144 * Math.PI
    },
    restCircle() {
      return this.isWorking
        ? this.circleLength * ((this.workTime - 1) / this.settingTime)
        : this.circleLength
    },
    circleColor() {
      return !this.autoStart ? '#4d6fcc' : '#4dbb91'
    },
  },
  methods: {
    countDown() {
      this.workTimer = setInterval(() => {
        this.workTime -= 1

        if (this.workTime === 0) {
          this.$emit('done', {
            startTime: this.startTime,
            endTime: new Date(),
          })
          clearInterval(this.workTimer)
        }
      }, 1000)
    },
    startTimer() {
      if (!this.isWorking) {
        this.startTime = new Date()
      }
      this.isPause = false
      this.countDown()
    },
    pauseTimer() {
      clearInterval(this.workTimer)
      this.isPause = true
    },
    cancelTimer() {
      clearInterval(this.workTimer)
      this.workTimer = null
      this.workTime = this.settingTime
    },
    skipTimer() {
      this.$emit('done', { startTime: this.startTime, endTime: new Date() })
      clearInterval(this.workTimer)
    },
  },
  mounted() {
    this.workTime = this.settingTime

    // auto start after 2sec
    if (this.autoStart) {
      setTimeout(() => {
        if (!this.workTimer) {
          this.startTimer()
        }
      }, 2000)
    }
  },
}
</script>

<style lang="scss" scoped>
.pomodoro-timer {
  display: flex;
  justify-content: center;
  align-items: center;

  &--time-group {
    position: relative;
    width: 300px;
    height: 300px;
    margin: 0 auto;
  }

  &--time {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 3rem;
  }

  &--circle {
    transform: rotate(-90deg);

    .circle-animation {
      transition: 1000ms linear;
    }
  }

  &--button-group {
    margin: -10px auto 0;
    width: 300px;
    display: flex;
    flex-flow: row-reverse;
    justify-content: space-between;
    align-items: center;
  }
  &--button {
    width: 80px;
    height: 80px;
    font-size: 1.2rem;
    border-radius: 50%;
    /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.6); */
    outline: none;
    cursor: pointer;
    transition: 600ms ease-in-out;
    background-color: transparent;
    /* box-shadow: none; */
  }
  &--button-orange {
    border: 1px solid #ffa033;
    color: #ffa033;
    &:hover {
      background-color: #eeb56b;
      color: #7e4401;
    }
  }
  &--button-gray {
    border: 1px solid #c5c0c0;
    color: #c5c0c0;
    &:hover {
      background-color: #928d8d;
      color: #3a3434;
    }
  }
}
</style>
