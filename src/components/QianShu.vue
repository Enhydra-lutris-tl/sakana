<template>
<div class="back-box" ref="backBox" @click="bounceClick">
  <img :src="imgData.imgSrc" alt="111">
</div>
</template>

<script>
import {ref,reactive,onUnmounted} from 'vue'
export default {
  name: "QianShu",
  props:['imgLocation'],
  setup(){
    const backBox = ref()
    const imgData = reactive({
      imgSrc : require('@/assets/img/海鳗.png')
    })
    const toShow = ref(1)
    // onMounted(()=>{
    //   backBox.value.style.top = props.imgLocation.imgY - 190 + 'px'
    //    backBox.value.style.left = props.imgLocation.imgX - 108 + 'px'
    // })
    function bounceClick(){
        backBox.value.classList.add('active')
      setTimeout(()=>{
        backBox.value.classList.remove('active')
      },2000)
    }
    onUnmounted(()=>{
      console.log('组件卸载了')
    })
    return{
      backBox,
      imgData,
      toShow,
      bounceClick
    }
  }
}
</script>

<style scoped>
.back-box{
  position: absolute;
  top: 50%;
  left: 50%;
  height: 200px;
  width: 230px;
  transform-origin: 50% 98%;

}
.back-box img{
  height: 100%;
}
.active{
  animation: bounce .1s infinite;
}
@keyframes bounce {
  0%{
    transform: translate(0,0) rotate(0deg);
  }
  25%{
    transform: translate(0,-30px) rotate(0deg);
  }
  50%{
    transform: translate(0,-35px) rotate(5deg);
  }
  75%{
    transform: translate(0,-10px) rotate(-5deg);
  }
  100%{
    transform: translate(0,0) rotate(5deg);
  }
}
</style>