<template>
  <div class="canvas-back">
    <canvas style="border: red 1px solid;position:absolute;top: 0;left: 0" id="fuck"></canvas>
    <div class="sakana-img" ref="sakanaImg">
      <img :src="imgSrc" alt="111111" draggable="false">
    </div>
  </div>
</template>

<script>
import {onMounted, reactive, ref} from "vue";

export default {
  name: "NewCanvas",

  setup() {
    const sakanaImg = ref()
    const imgShow = ref(0)
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
    //
    function Sakana() {
      //弹簧相关
      this.H = 600
      this.minH = 300
      this.maxH = 900

      this.maxW = 300 //最大横向距离
      // 弹簧低端位置
      this.Tx = 300
      this.Ty = 900 //例900


      //释放坐标
      this.maxR = 60
      this.nx = null
      this.ny = null

      this.vY = null
      this.vX = null

      this.dgY = 0.98 //衰减系数
      this.dgX = 0.96

      // 初始计算属性属性
      this.bounce = function () {
        // 人物初始状态
        this.x = this.Tx //x坐标
        this.y = this.Ty - this.H - 100//y坐标
      }

    }

    // function beval(x, y) {
    //   const r = Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2))
    //   const sinOfX = y / r
    //
    //   return Math.round((Math.asin(sinOfX) * 180) / Math.PI)
    // }


    onMounted(() => {
      const canvas = document.getElementById('fuck')
      canvas.height = document.body.offsetHeight
      canvas.width = 900
      const ctx = canvas.getContext('2d')
      const sakana = new Sakana()
      sakana.bounce()
      sakanaImg.value.style.top = sakana.y + 'px'
      sakanaImg.value.style.left = sakana.x + 'px'

      function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.beginPath()
        ctx.lineWidth = 10
        const toX = sakana.x + 115
        const drawX = sakanaImg.value.offsetLeft + 115
        const drawY = sakanaImg.value.offsetTop + 200
        ctx.moveTo(toX, sakana.Ty);
        ctx.quadraticCurveTo(toX, sakana.Ty-100, drawX, drawY);
        ctx.stroke();
      }

      draw()


      sakanaImg.value.onmousedown = () => {
        console.log('鼠标按下')
        imgShow.value = 1

        onmousemove = (e) => {
          sakana.ny = e.y - 100
          sakana.nx = e.x - 115
          if (sakana.ny >= sakana.Ty - sakana.minH) {
            sakana.ny = sakana.Ty - sakana.minH
          } else if (sakana.ny <= sakana.Ty - sakana.maxH) {
            sakana.ny = sakana.Ty - sakana.maxH
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
          onmousemove = null
          imgShow.value = 0;

          //弹性运动模板
          (function elasticityMode() {
            sakana.vY += (sakanaImg.value.offsetTop - sakana.y) / 10
            sakana.vX += (sakanaImg.value.offsetLeft - sakana.x) / 10

            sakana.vY *= sakana.dgY
            sakana.vX *= sakana.dgX

            sakanaImg.value.style.top = sakanaImg.value.offsetTop - sakana.vY + 'px'
            sakanaImg.value.style.left = sakanaImg.value.offsetLeft - sakana.vX + 'px'

            draw()

            sakana.nx = sakanaImg.value.offsetLeft
            sakana.r = (sakana.nx - sakana.Tx)/5
            sakanaImg.value.style.transform = `rotate(${sakana.r}deg)`
            const anm = requestAnimationFrame(elasticityMode)
            if (Math.abs(sakana.vX) <= 1 && Math.abs(sakanaImg.value.offsetLeft - sakana.vX) <= 1) {
              sakanaImg.value.style.top = sakana.y + 'px'
              sakana.v = 0
              cancelAnimationFrame(anm)
            }
            if (imgShow.value === 1) {
              cancelAnimationFrame(anm)
            }
          })();


          onmouseup = null
          console.log('鼠标松开', `y坐标为${e.y - 100})`)
        }
      }
    })


    return {
      lineStyle,
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
  height: 200px;
  width: 230px;
  transform-origin: 50% 98%;
}

.sakana-img img {
  height: 100%;
}
</style>