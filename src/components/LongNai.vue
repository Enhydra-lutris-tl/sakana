<template>
  <div class="back-box" ref="backBox" @click="ceshiClick">
    <img :src="imgData.imgSrc" alt="111">
  </div>
</template>

<script>
import {ref, reactive, onUnmounted} from 'vue'

export default {
  name: "LongNai",

  setup() {
    const backBox = ref()
    const imgData = reactive({
      imgSrc: require('@/assets/img/鱼.png')
    })
    const toShow = ref(1)
    // onMounted(()=>{
    //   backBox.value.style.top = props.imgLocation.imgY - 190 + 'px'
    //    backBox.value.style.left = props.imgLocation.imgX - 108 + 'px'
    // })
    function ceshiClick() {
      backBox.value.classList.add('active')
      setTimeout(() => {
        backBox.value.classList.remove('active')
      }, 1800)
    }

    onUnmounted(() => {
      console.log('组件卸载了')
    })
    return {
      backBox,
      imgData,
      toShow,
      ceshiClick
    }
  }
}
</script>

<style scoped>
.back-box {
  position: absolute;
  top: 50%;
  left: 38%;
  height: 200px;
  width: 230px;
  transform-origin: 50% 98%;

}

.back-box img {
  height: 100%;
}

.active {
  animation: ceshi 0.3s infinite;
}

@keyframes ceshi {
  0% {
    transform: rotate(-25deg);
  }
  50% {
    transform: rotate(25deg);
  }
  100% {
    transform: rotate(-25deg);
  }
}
</style>