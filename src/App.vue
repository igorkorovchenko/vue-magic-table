<template>
  <div id="app">
    <MagicTable tabledata="/static/data.json" />
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    MagicTable: () => import('./components/MagicTable')
  }
}
</script>

<style scoped>
#app {
  margin: 0 auto;
  padding: 0;
  width: 100%;
}
</style>
