<template>
<div class="back" ref="nack">

</div>
</template>

<script>
import {ref} from "vue";

export default {
  name: "NewBack",
  setup(){
    const nack = ref()
    const imgSrc = ''
    function imgInput(){
      const file = this.$refs.imageInput.files[0]
      const fr = new FileReader()
      fr.onload = (e) => {
        this.imgSrc = e.target.result
        console.log(imgSrc)
        localStorage.setItem('newImage',imgSrc)
        this.imgWidthToHeight()
      }
      fr.readAsDataURL(file)
    }
    return{
      imgInput,
      nack,
      imgSrc
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
</style>