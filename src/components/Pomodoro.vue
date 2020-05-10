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

    <!-- task -->
    <div class="task">
      <label for="task">Task</label>
      <input type="text" id="task" v-model="task" />
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
      task
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
        task
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
      task: '',
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
            task: this.task,
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

      this.task = ''
      this.isBreak = true
    },
  },
}
</script>

<style lang="scss" scoped>
.task {
  margin: 0 auto;
  margin-top: 2rem;

  label {
    margin-right: 0.5rem;
  }

  input {
    border: none;
    border-bottom: 1px solid #d0d2d4;
    background-color: transparent;
    color: #d0d2d4;
    font-size: 1rem;
    padding: 0.5rem;
    outline: none;
    width: 250px;
  }
}

.pomodoro-list {
  margin: 0 auto;
  margin-top: 2rem;
}
</style>
