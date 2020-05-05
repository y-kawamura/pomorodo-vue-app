<template>
  <PomodoroBaseTimer
    :settingTime="settingTime"
    :autoStart="true"
    :skip="true"
    @done="doneTimer"
  />
</template>

<script>
import PomodoroBaseTimer from './PomodoroBaseTimer'
import { SHORT_BREAK_TIME, LONG_BREAK_TIME } from '@/config'

export default {
  props: {
    isLongBreak: {
      type: Boolean,
      default: false,
    },
  },
  components: {
    PomodoroBaseTimer,
  },
  computed: {
    settingTime() {
      return this.isLongBreak ? LONG_BREAK_TIME : SHORT_BREAK_TIME
    },
  },
  methods: {
    doneTimer({ startTime, endTime }) {
      this.$emit('done', { startTime, endTime })
    },
  },
}
</script>

<style></style>
