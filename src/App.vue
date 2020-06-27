<template>
  <div id="app">
    <router-view/>
    <div id="refresh" v-if="updateExists">
      <p>
      Nova atualização encontrada!
      </p>
      <button @click="refreshApp">
        Atualizar
      </button>
    </div>
  </div>
</template>
<script charset="utf-8">
export default {
  name: 'app',
  data () {
    return {
      refreshing: false,
      updateExists: false,
      registration: null
    }
  },
  created () {
    document.addEventListener('swUpdated', this.showRefreshUI, { once: true })
    navigator.serviceWorker.addEventListener('controllerchange', () => {
      if (this.refreshing) return
      this.refreshing = true
      window.location.reload()
    })
  },
  methods: {
    showRefreshUI (e) {
      this.registration = e.detail
      this.updateExists = true
    },
    refreshApp () {
      this.updateExists = false
      if (!this.registration || !this.registration.waiting) return
      // send message to SW to skip the waiting and activate the new SW
      this.registration.waiting.postMessage('skipWaiting')
    }
  }
}
</script>
<style lang="scss">
  @import "scss/main.scss";
</style>
