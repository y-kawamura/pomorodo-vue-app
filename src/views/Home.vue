<template>
  <div class="home">
    <Pomodoro />
    <!-- <div v-for="pomodoro in allPomodoros" :key="pomodoro.id">
      <h1>Pomodoro : {{ pomodoro.day }}</h1>
      <div v-for="session in pomodoro.sessions" :key="session.id">
        {{ session.id }}: {{ session.startTime }} - {{ session.endTime }}
      </div>
    </div> -->
  </div>
</template>

<script>
// import gql from 'graphql-tag'
import Pomodoro from '@/components/Pomodoro'

// const ALL_POMODOROS_QUERY = gql`
//   {
//     allPomodoros {
//       day
//       sessions {
//         id
//         task
//         startTime
//         endTime
//       }
//     }
//   }
// `

export default {
  name: 'Home',
  components: {
    Pomodoro,
  },
  // apollo: {
  //   allPomodoros: {
  //     query: ALL_POMODOROS_QUERY,
  //   },
  // },
}
</script>
