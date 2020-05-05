<template>
  <PomodoroBaseTimer :settingTime="settingTime" @done="doneTimer" />
</template>

<script>
import PomodoroBaseTimer from './PomodoroBaseTimer'
import { WORK_TIME } from '@/config'

export default {
  components: {
    PomodoroBaseTimer,
  },
  data() {
    return {
      settingTime: WORK_TIME,
    }
  },
  methods: {
    doneTimer({ startTime, endTime }) {
      this.$emit('done', { startTime, endTime })
    },
  },
}
</script>

<style></style>
