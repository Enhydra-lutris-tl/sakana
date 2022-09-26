<template>
  <div class="canvas-back">
    <div ref="sakanaStop" class="stop-button">
      <i class="iconfont icon-zanting"></i>
    </div>
    <canvas style="position:absolute;top: 0;left: 0" id="sakanaCanvas"></canvas>
    <div class="sakana-img" ref="sakanaImg">
      <img :src="imgSrc" alt="111111">
    </div>
  </div>
</template>

<script>
import {onMounted, ref, reactive, computed} from "vue";

export default {
  name: "NewSakana",

  setup() {

    const sakanaStop = ref()
    const sakanaImg = ref()
    const musicPlay = ref(true)//播放声音
    const imgShow = ref(0) //1为停止定时器
    const imgSrc = require('@/assets/img/泷奈.png')
    const Sakana = reactive({
      //弹簧相关
      H: 500,
      minH: 400,
      maxH: 600,
      maxW: 300,//最大横向距离

      // 弹簧低端位置
      Tx: document.documentElement.offsetWidth / 2 - 175,
      Ty: document.documentElement.offsetHeight,//例900

      //移动实时坐标，释放坐标
      maxR: 66,
      nx: 1,
      ny: 1,
      //实时速度
      vY: 1,
      vX: 1,

      //加速减速系数
      xY: 30,
      xX: 50,

      //衰减系数
      dgY: 0.98,
      dgX: 0.99,
    })

    const bounce = computed(() => {
      return {
        x: Sakana.Tx,
        y: Sakana.Ty - Sakana.H - 150
      }
    })

    onMounted(() => {
      const canvas = document.getElementById('sakanaCanvas')
      canvas.height = document.body.offsetHeight
      canvas.width = document.documentElement.offsetWidth
      const ctx = canvas.getContext('2d')

      const sakanaMusic = new Audio()
      sakanaMusic.src = require('@/assets/music/sakana.mp3')


      sakanaImg.value.style.top = bounce.value.y + 'px'
      sakanaImg.value.style.left = bounce.value.x + 'px'

      // 画弹簧
      function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.beginPath()
        ctx.lineWidth = 15
        const toX = bounce.value.x + 166
        const drawX = sakanaImg.value.offsetLeft + 166
        const drawY = sakanaImg.value.offsetTop + 296
        ctx.moveTo(toX, Sakana.Ty);
        ctx.quadraticCurveTo(toX, Sakana.Ty - 100, drawX, drawY);
        ctx.stroke();
      }

      draw()

      sakanaImg.value.onmousedown = () => {
        console.log('鼠标按下')
        imgShow.value = 1

        onmousemove = (e) => {
          Sakana.ny = e.y - 150
          Sakana.nx = e.x - 175
          if (Sakana.ny >= Sakana.Ty - Sakana.minH - 150) {
            Sakana.ny = Sakana.Ty - Sakana.minH - 150
          } else if (Sakana.ny <= Sakana.Ty - Sakana.maxH - 150) {
            Sakana.ny = Sakana.Ty - Sakana.maxH - 150
          }

          if (Sakana.nx >= bounce.value.x + Sakana.maxW) {
            Sakana.nx = bounce.value.x + Sakana.maxW

          } else if (Sakana.nx <= bounce.value.x - Sakana.maxW) {
            Sakana.nx = bounce.value.x - Sakana.maxW
          }

          Sakana.r = (Sakana.nx - Sakana.Tx) / 5

          sakanaImg.value.style.left = Sakana.nx + 'px'
          sakanaImg.value.style.top = Sakana.ny + 'px'

          sakanaImg.value.style.transform = `rotate(${Sakana.r}deg)`
          draw()

        }

        onmouseup = (e) => {

          onmousemove = null

          imgShow.value = 0;
          if (musicPlay.value) {
            sakanaMusic.play()
          }

          //弹性运动模板
          (function elasticityMode() {
            //循环执行 先加速后减速运动
            Sakana.vY += (sakanaImg.value.offsetTop - bounce.value.y) / Sakana.xY
            Sakana.vX += (sakanaImg.value.offsetLeft - bounce.value.x) / Sakana.xX

            Sakana.vY *= Sakana.dgY
            Sakana.vX *= Sakana.dgX

            sakanaImg.value.style.top = sakanaImg.value.offsetTop - Sakana.vY + 'px'
            sakanaImg.value.style.left = sakanaImg.value.offsetLeft - Sakana.vX + 'px'
            //弹簧顶端跟随移动
            draw()

            // 人物旋转角度分配
            Sakana.nx = sakanaImg.value.offsetLeft
            Sakana.r = (Sakana.nx - Sakana.Tx) * Sakana.maxR / Sakana.maxW
            sakanaImg.value.style.transform = `rotate(${Sakana.r}deg)`
            const anm = requestAnimationFrame(elasticityMode)
            //当达到某些条件时停止定时器
            // if (Math.abs(Sakana.vX) <= 1 && Math.abs(sakanaImg.value.offsetLeft - Sakana.vX) <= 1) {
            //   sakanaImg.value.style.top = Sakana.y + 'px'
            //   Sakana.v = 0
            //   cancelAnimationFrame(anm)
            // }
            if (imgShow.value === 1) {
              cancelAnimationFrame(anm)
            }
          })();
          // 取消鼠标up事件
          sakanaStop.value.onclick = function () {
            imgShow.value = 1
            sakanaImg.value.style.top = bounce.value.y + 'px'
            sakanaImg.value.style.left = bounce.value.x + 'px'
            Sakana.v = 0
          }
          onmouseup = null
          console.log('鼠标松开', `y坐标为${e.y - 100})`)
        }
      }
    })

    //壁纸相关设置属性
    window.wallpaperPropertyListener = {
      applyUserProperties: function (properties) {

        if (properties.newxx) {
          Sakana.xX = properties.newxx.value
        }
        if (properties.newdgx) {
          Sakana.dgX = properties.newdgx.value
        }
        if (properties.newxy){
          Sakana.xY = properties.newdxy.value
        }
        if (properties.newdgy) {
          Sakana.dgY = properties.newdgy.value
        }
        if (properties.musicchecked) {
          musicPlay.value = properties.musicchecked.value
        }
      },
    };

    return {
      sakanaImg,
      imgSrc,
      bounce,
      sakanaStop,
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
.stop-button{
  position: absolute;
  top: 20%;
  right: 4px;
  z-index: 5;
  height: 30px;
  width: 30px;
  line-height: 30px;
  border-radius: 4px;
  border: solid 2px rgba(255, 255, 255, 0.5);
  transition: 0.5s;
  color: white;
}
.stop-button:hover{
  background: rgba(255, 255, 255, 0.5);
}
.stop-button .iconfont{
  font-size: 24px;
}
</style>