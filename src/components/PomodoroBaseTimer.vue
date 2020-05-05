<template>
  <div>
    <span class="time">{{ workTimerMin }} : {{ workTimerSec }}</span>
    <!-- start -->
    <button v-show="!autoStart" :disabled="!!workTimer" @click="startTimer">
      Start
    </button>
    <!-- pause -->
    <button v-show="!isPause" @click="pauseTimer">Pause</button>
    <button v-show="isPause" @click="restartTimer">Restart</button>
    <!-- stop -->
    <button v-show="!skip" @click="cancelTimer">Cancel</button>
    <button v-show="skip" @click="skipTimer">Skip</button>
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
  },
  methods: {
    countDown() {
      this.workTimer = setInterval(() => {
        this.workTime -= 1

        if (this.workTime === 0) {
          this.$emit('done', { startTime: this.startTime, endTime: new Date() })
          clearInterval(this.workTimer)
          this.workTime = this.settingTime
        }
      }, 1000)
    },
    startTimer() {
      this.startTime = new Date()
      this.countDown()
    },
    pauseTimer() {
      clearInterval(this.workTimer)
      this.isPause = true
    },
    restartTimer() {
      this.isPause = false
      this.countDown()
    },
    cancelTimer() {
      clearInterval(this.workTimer)
      this.workTime = this.settingTime
    },
    skipTimer() {
      this.$emit('done', { startTime: this.startTime, endTime: new Date() })
      clearInterval(this.workTimer)
      this.workTime = this.settingTime
    },
  },
  watch: {
    settingTime(newVal) {
      this.workTime = newVal
    },
  },
  mounted() {
    if (this.autoStart) {
      this.startTimer()
    }
  },
}
</script>

<style lang="scss" scoped>
.time {
  display: block;
  font-size: 5rem;
}
</style>
