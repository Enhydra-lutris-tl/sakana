<template>
  <div class="canvas-back">
    <canvas style="position:absolute;top: 0;left: 0" id="sakanaCanvas"></canvas>
    <div class="sakana-img" ref="sakanaImg">
      <img :src="imgSrc" alt="111111">
    </div>
  </div>
</template>

<script>
import {onMounted, ref} from "vue";

export default {
  name: "NewSakana",

  setup() {
    const sakanaImg = ref()
    const imgShow = ref(0) //1为停止定时器
    const imgSrc = require('@/assets/img/泷奈.png')

    // 角色模板
    function Sakana() {
      //弹簧相关
      this.H = 500
      this.minH = 400
      this.maxH = 600

      this.maxW = 300 //最大横向距离
      // 弹簧低端位置
      this.Tx = document.documentElement.offsetWidth/2 - 175
      this.Ty = document.documentElement.offsetHeight //例900


      //释放坐标
      this.maxR = 66
      this.nx = null
      this.ny = null

      this.vY = null
      this.vX = null

      // 初始计算属性属性
      this.bounce = function () {
        // 人物初始状态
        this.x = this.Tx //x坐标
        this.y = this.Ty - this.H - 150//y坐标
      }

    }


    onMounted(() => {
      const canvas = document.getElementById('sakanaCanvas')
      canvas.height = document.body.offsetHeight
      canvas.width = document.documentElement.offsetWidth
      const ctx = canvas.getContext('2d')

      const sakanaMusic = new Audio()
      sakanaMusic.src = require('@/assets/music/sakana.mp3')


      const sakana = new Sakana()
      sakana.bounce()
      sakanaImg.value.style.top = sakana.y + 'px'
      sakanaImg.value.style.left = sakana.x + 'px'

      // 画弹簧
      function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.beginPath()
        ctx.lineWidth = 15
        const toX = sakana.x + 166
        const drawX = sakanaImg.value.offsetLeft + 166
        const drawY = sakanaImg.value.offsetTop + 296
        ctx.moveTo(toX, sakana.Ty);
        ctx.quadraticCurveTo(toX, sakana.Ty-100, drawX, drawY);
        ctx.stroke();
      }

      draw()


      sakanaImg.value.onmousedown = () => {
        console.log('鼠标按下')
        imgShow.value = 1

        onmousemove = (e) => {
          sakana.ny = e.y - 150
          sakana.nx = e.x - 175
          if (sakana.ny >= sakana.Ty - sakana.minH - 150) {
            sakana.ny = sakana.Ty - sakana.minH - 150
          } else if (sakana.ny <= sakana.Ty - sakana.maxH - 150) {
            sakana.ny = sakana.Ty - sakana.maxH - 150
          }

          if (sakana.nx >= sakana.x + sakana.maxW) {
            sakana.nx = sakana.x + sakana.maxW

          } else if (sakana.nx <= sakana.x - sakana.maxW) {
            sakana.nx = sakana.x - sakana.maxW
          }

          sakana.r = (sakana.nx - sakana.Tx)/5

          sakanaImg.value.style.left = sakana.nx + 'px'
          sakanaImg.value.style.top = sakana.ny + 'px'

          sakanaImg.value.style.transform = `rotate(${sakana.r}deg)`
          draw()

        }

        onmouseup = (e) => {
          //加速减速系数
          const xY = 30,
              xX = 50

          //衰减系数
          const dgY = 0.98,
          dgX = 0.998
          onmousemove = null

          imgShow.value = 0

          sakanaMusic.play();
          //弹性运动模板
          (function elasticityMode() {
            //循环执行 先加速后减速运动
            sakana.vY += (sakanaImg.value.offsetTop - sakana.y) / xY
            sakana.vX += (sakanaImg.value.offsetLeft - sakana.x) / xX

            sakana.vY *= dgY
            sakana.vX *= dgX

            sakanaImg.value.style.top = sakanaImg.value.offsetTop - sakana.vY + 'px'
            sakanaImg.value.style.left = sakanaImg.value.offsetLeft - sakana.vX + 'px'
            //弹簧顶端跟随移动
            draw()

            // 人物旋转角度分配
            sakana.nx = sakanaImg.value.offsetLeft
            sakana.r = (sakana.nx - sakana.Tx)*sakana.maxR/sakana.maxW
            sakanaImg.value.style.transform = `rotate(${sakana.r}deg)`
            const anm = requestAnimationFrame(elasticityMode)
            //当达到某些条件时停止定时器
            // if (Math.abs(sakana.vX) <= 1 && Math.abs(sakanaImg.value.offsetLeft - sakana.vX) <= 1) {
            //   sakanaImg.value.style.top = sakana.y + 'px'
            //   sakana.v = 0
            //   cancelAnimationFrame(anm)
            // }

            if (imgShow.value === 1) {
              cancelAnimationFrame(anm)
            }

          })();
          // 取消鼠标up事件
          onmouseup = null
          console.log('鼠标松开', `y坐标为${e.y - 100})`)
        }
      }
    })


    return {
      sakanaImg,
      imgSrc,
    }
  }
}
</script>

<style scoped>
.sakana-img {
  position: absolute;
  top: 50%;
  left: 38%;
  height: 300px;
  width: 350px;
  transform-origin: 166px 98%;
}

.sakana-img img {
  height: 100%;
  pointer-events: none;
  user-select: none;
}
</style>