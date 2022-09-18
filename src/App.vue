<template>
  <NewCanvas></NewCanvas>
  <botton @click="yigeanniu">点击</botton>
</template>

<script>
import {ref, reactive} from "vue";
import NewCanvas from '@/components/NewCanvas'
export default {
  name: 'App',
  components: {
    NewCanvas
  },
  setup() {
    const childOne = ref()
    const childTwo = ref()
    const toShow = ref(0)
    const imgLocation = reactive({
      imgX: 10,
      imgY: 10
    })
    // function imgShow() {
    //   toShow.value = 1
    //   setTimeout(() => {
    //     toShow.value = 0
    //   }, 900)
    // }
    // window.onclick =function (event){
    //   console.log(event.x, event.y)
    //   imgLocation.imgX = event.x
    //   imgLocation.imgY =event.y
    //   imgShow()
    // }
    function yigeanniu() {
      childOne.value.ceshiClick()
      childTwo.value.bounceClick()
    }

    return {
      childOne,
      childTwo,
      toShow,
      imgLocation,
      yigeanniu
    }
  }
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
