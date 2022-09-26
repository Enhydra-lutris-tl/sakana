<template>
<div class="back" ref="nack">
  <input type="file" class="img-input" ref="imageInput" @change="imgInput">
  <img :src="imgSrc" alt="" ref="imgBack">
  <div class="input-button" @click="NewImgInput">
    <i class="iconfont icon-icon-test2"></i>
  </div>
</div>
</template>

<script>
import {onMounted, ref} from "vue";

export default {
  name: "NewBack",
  setup(){
    const imageInput = ref()
    const imgBack = ref()
    const nack = ref()
    const ratio = ref()
    const imgSrc = ref(localStorage.getItem('newImage'))

    window.onresize=()=>{
      imgWidthToHeight()
    }

    //自适应显示窗口变化
    function imgWidthToHeight(){
      const EW = document.documentElement.offsetWidth,
          EH = document.documentElement.offsetHeight

      if ( EW/ratio.value < EH){
        imgBack.value.style.height = EH + 'px'
        imgBack.value.style.width = 'auto'
      }else {
        imgBack.value.style.width = EW + 'px'
        imgBack.value.style.height = 'auto'
      }

    }

    function getRatio(){
      const newRatio = new Image()
      newRatio.src = imgSrc.value

      // 注意加载图片需要时间
      newRatio.onload = ()=>{
        ratio.value = newRatio.width/newRatio.height
        imgWidthToHeight()
      }

    }
    function imgInput(){
      const file = imageInput.value.files[0]
      const fr = new FileReader()
      fr.onload = (e) => {
        imgSrc.value = e.target.result
        localStorage.setItem('newImage',e.target.result)
        getRatio()
      }
      fr.readAsDataURL(file)

    }

    function NewImgInput(){
      imageInput.value.click()
    }
    onMounted(()=>{
      getRatio()
    })
    return{
      imgBack,
      imgInput,
      nack,
      ratio,
      imgSrc,
      imageInput,
      NewImgInput
    }
  }
}
</script>

<style scoped>
.back{
  height: 100vh;
  width: 100vw;
  background: lightskyblue;
}
.back img{
  pointer-events: none;
  user-select: none;
  transition: 0.1s;
}

.input-button{
  position: absolute;
  top: 14%;
  right: 4px;
  z-index: 3;
  height: 30px;
  width: 30px;
  line-height: 30px;
  border-radius: 4px;
  border: solid 2px rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: 0.5s;
  color: white;
}
.input-button .iconfont{
  font-size: 24px;
}
.input-button:hover{
  background: rgba(255, 255, 255, 0.5);

}

.img-input{
  display: none;
  position: absolute;
  z-index: 2;
}
</style>