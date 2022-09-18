<template>
  <div class="canvas-back">
    <canvas height="600" width="600" style="border: red 1px solid" id="fuck"></canvas>
    <div class="sakana-img" ref="sakanaImg">
      <img :src="imgSrc" alt="111111">
    </div>
  </div>
</template>

<script>
import {onMounted, reactive, ref} from "vue";

export default {
  name: "NewCanvas",
  setup() {
    const a = ref(200)
    const sakanaImg = ref()
    const imgSrc = require('@/assets/img/鱼.png')
    const lineStyle = reactive(
        {
          fx: 300,
          fy: 600,
          tx: 200,
          ty: 400,
          minX: 100,
          minY: 500,
          maxX: 500,
          maxY: 300
        }
    )

    // 角色模板
    function Sakana() {
      this.gravity = 5 //重力
      this.x = null //x坐标
      this.y = null //y坐标
      this.nx = null //释放坐标
      this.ny = null
      this.k = null //弹力系数
      this.dg = null //衰减系数





      this.bounce = function () {
        if (this.k * (this.ny - this.y) > 0) {
          this.F = Math.abs(this.k * (this.ny - this.y))
          this.stress = this.F - this.gravity
        } else {
          this.F = Math.abs(this.k * (this.ny - this.y))
          this.stress = this.F + this.gravity
        }
        this.v = this.stress*0.1
      }
    }


    onMounted(() => {
      const canvas = document.getElementById('fuck')
      const ctx = canvas.getContext('2d')
      const ceshi = new Sakana()
      // 鼠标按住移动事件
      onmousedown = (e) => {
        console.log('鼠标按下', e.x, e.y)
        ceshi.y = e.y - 100
        sakanaImg.value.style.top = e.y - 100 + 'px'


        const x = setInterval(() => {
          ctx.clearRect(0, 0, 600, 600)
          ctx.beginPath()
          ctx.moveTo(300, 600);
          ctx.quadraticCurveTo(300, 420, a.value, 400);
          ctx.stroke();
          a.value += 10
          if (a.value === 400) {
            clearInterval(x)
            console.log('动画结束')
            a.value = 200
          }
        }, 1000 / 60)


        onmousemove = (e) => {
          sakanaImg.value.style.top = e.y - 100 + 'px'
        }


        onmouseup = (e) => {
          ceshi.ny = e.y - 100
          onmousemove = null
          ceshi.k = 5
          ceshi.dg = 0.98
          ceshi.bounce()
          //回弹速度
          const a = setInterval(()=>{
            ceshi.v *= ceshi.dg
            const yuanchang =(ceshi.ny - ceshi.y)
            console.log('长度',yuanchang)
            if (sakanaImg.value.offsetTop >= yuanchang/2 + ceshi.ny){
              sakanaImg.value.style.top = sakanaImg.value.offsetTop - ceshi.v +'px'
            }else{
              sakanaImg.value.style.top = sakanaImg.value.offsetTop + ceshi.v +'px'
            }
            if (ceshi.v < 1){
              clearInterval(a)
            }
          },1000/60)
          console.log(ceshi)
          console.log('取消', ceshi.F)



        }
      }
    })


    return {
      lineStyle,
      sakanaImg,
      imgSrc
    }
  }
}
</script>

<style scoped>
.sakana-img {
  position: absolute;
  top: 50%;
  left: 38%;
  height: 200px;
  width: 230px;
  transform-origin: 50% 98%;

}

.sakana-img img {
  height: 100%;
}
</style>