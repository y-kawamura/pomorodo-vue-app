<template>
  <div>
    <div v-for="session in sessions" :key="session.id">
      <span>{{ session.id }}:</span>
      <span
        >{{ hourMin(session.startTime) }} -
        {{ hourMin(session.endTime) }}
      </span>
    </div>
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
    hourMin() {
      return dateTime => {
        const time = new Date(dateTime)
        return `${time.getHours()} : ${time.getMinutes()}`
      }
    },
  },
}
</script>

<style lang="scss" scoped></style>
