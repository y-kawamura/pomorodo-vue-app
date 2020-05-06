<template>
  <div>
    <div v-if="!isBreak" class="pomodoro-timer">
      <h1>Pomodoro #{{ sessionNumber }}</h1>
      <PomodoroWorkTimer @done="donePomodoro" />
    </div>
    <div v-if="isBreak" class="pomodoro-timer">
      <h1>Break Time</h1>
      <PomodoroBreakTimer :is-long-break="isLongBreak" @done="doneBreak" />
    </div>

    <div v-if="todayPomodoro" class="pomodoro-list">
      <PomodoroTodayList :sessions="todayPomodoro.sessions" />
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'
import PomodoroWorkTimer from './PomodoroWorkTimer'
import PomodoroBreakTimer from './PomodoroBreakTimer'
import PomodoroTodayList from './PomodoroTodayList'
import { LONG_BREAK_SPAN } from '@/config'

const ADD_POMODORO_MUTATION = gql`
  mutation($input: AddPomodoroInput!) {
    addPomodoro(input: $input) {
      id
      startTime
      endTime
    }
  }
`

const TODAY_POMODORO_QUERY = gql`
  query {
    todayPomodoro {
      sessions {
        id
        startTime
        endTime
      }
    }
  }
`

export default {
  components: {
    PomodoroWorkTimer,
    PomodoroBreakTimer,
    PomodoroTodayList,
  },
  apollo: {
    todayPomodoro: {
      query: TODAY_POMODORO_QUERY,
    },
  },
  data() {
    return {
      todayPomodoro: null,
      isBreak: false,
    }
  },
  computed: {
    isLongBreak() {
      return this.todayPomodoro
        ? this.todayPomodoro.sessions.length % LONG_BREAK_SPAN === 0
        : false
    },
    sessionNumber() {
      return this.todayPomodoro ? this.todayPomodoro.sessions.length + 1 : 1
    },
  },
  methods: {
    doneBreak() {
      new Notification(`休憩時間が終了しました`)
      this.isBreak = false
    },
    async donePomodoro({ startTime, endTime }) {
      new Notification(
        `お疲れ様です。${this.sessionNumber}回目のポモドーロが終了しました`
      )

      await this.$apollo.mutate({
        mutation: ADD_POMODORO_MUTATION,
        variables: {
          input: {
            day: new Date(),
            startTime,
            endTime,
          },
        },
        update: (cache, { data: { addPomodoro } }) => {
          if (!this.todayPomodoro) {
            this.$apollo.queries.todayPomodoro.refetch()
          } else {
            const data = cache.readQuery({ query: TODAY_POMODORO_QUERY })
            data.todayPomodoro.sessions.push(addPomodoro)
            cache.writeQuery({ query: TODAY_POMODORO_QUERY, data })
          }
        },
      })

      this.isBreak = true
    },
  },
}
</script>

<style lang="scss" scoped>
.pomodoro-list {
  margin-top: 5rem;
}
</style>
