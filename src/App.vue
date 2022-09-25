<template>
  <NewSakana></NewSakana>
  <NewBack></NewBack>
</template>

<script>
import NewSakana from '@/components/NewSakana'
import NewBack from '@/components/NewBack'

export default {
  name: 'App',
  components: {
    NewSakana,
    NewBack
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  text-align: center;
}

body {
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}
</style>
