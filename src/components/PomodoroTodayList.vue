<template>
  <div>
    <table class="pomodoro-list">
      <tbody>
        <tr v-for="session in sessions" :key="session.id">
          <td>{{ session.id }}</td>
          <td class="time">
            {{ hourMin(session.startTime) }} - {{ hourMin(session.endTime) }}
          </td>
          <td class="task">{{ session.task || '-' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    sessions: {
      type: Array,
      required: true,
    },
  },
  computed: {
    zeroPadding() {
      return num => ('00' + num).slice(-2)
    },
    hourMin() {
      return dateTime => {
        const time = new Date(dateTime)
        return `
          ${this.zeroPadding(time.getHours())} : 
          ${this.zeroPadding(time.getMinutes())}
        `
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.pomodoro-list {
  margin: 0 auto;

  .time {
    width: 120px;
  }

  td {
    padding-right: 0.5rem;

    &:last-child {
      padding-right: 0;
    }
  }
}
</style>
