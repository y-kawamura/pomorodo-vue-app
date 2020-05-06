<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  methods: {
    notifyMe() {
      if (!('Notification' in window)) {
        alert('このブラウザはシステム通知をサポートしていません')
      } else if (Notification.permission === 'default') {
        Notification.requestPermission()
      }
    },
  },
  created() {
    this.notifyMe()
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
