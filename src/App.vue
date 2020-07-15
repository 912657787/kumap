<template>
  <div id="warp-box">
    <!--这里是全局视图层-->
    <router-view></router-view>
    <!-- 测试按钮 -->
    <div @click="show" ref="bar" class="bottom_bar"></div>
    <div v-drag ref="window" class="window">
      <div class="window_top" style="background-color: white; height: 30px; line-height: 30px; padding-left: 10px;">
        <span>运行日志</span>
        <span @click="unshow" class="unshow">—</span>
      </div>
      <div class="cmdBody">
        <div @click="setMock" style="cursor: pointer; text-align: center; padding: 20px; background-color: #01D5FF; color: black">点击测试修改mock数据</div>
      </div>
    </div>
  </div>
</template>
<script>
  import axios from 'axios';
  export default {
    name: 'warp-box',
    data() {
      return {

      }
    },

    mounted() {
      axios.get("http://127.0.0.1:3000/getIndexList")
      .then(res => {
        console.log(res)
      })
    },

    //注册局部组件指令
    directives: {
      drag: function(el) {
        const dialogHeaderEl = el.querySelector('.window_top')
        const dragDom = el
        dialogHeaderEl.style.cursor = 'move'

        // 获取原有属性 ie dom元素.currentStyle 火狐谷歌 window.getComputedStyle(dom元素, null);
        const sty = dragDom.currentStyle || window.getComputedStyle(dragDom, null)

        dialogHeaderEl.onmousedown = (e) => {
          dragDom.style.transition = "0s"
          // 鼠标按下，计算当前元素距离可视区的距离
          const disX = e.clientX - dialogHeaderEl.offsetLeft
          const disY = e.clientY - dialogHeaderEl.offsetTop

          // 获取到的值带px 正则匹配替换
          let styL, styT

          // 注意在ie中 第一次获取到的值为组件自带50% 移动之后赋值为px
          if (sty.left.includes('%')) {
            styL = +document.body.clientWidth * (+sty.left.replace(/\%/g, '') / 100)
            styT = +document.body.clientHeight * (+sty.top.replace(/\%/g, '') / 100)
          } else {
            styL = +sty.left.replace(/\px/g, '')
            styT = +sty.top.replace(/\px/g, '')
          }

          document.onmousemove = function(e) {
            // 通过事件委托，计算移动的距离
            const l = e.clientX - disX
            const t = e.clientY - disY

            // 移动当前元素
            dragDom.style.left = `${l + styL}px`
            dragDom.style.top = `${t + styT}px`

            // 将此时的位置传出去
            // binding.value({x:e.pageX,y:e.pageY})
          }

          document.onmouseup = function(e) {
            document.onmousemove = null
            document.onmouseup = null
          }
        }
      }
    },

    methods: {
      show: function() {
        var dom = this.$refs.bar
        var dom2 = this.$refs.window
        var x = -document.body.clientWidth / 2 + 100 + "px"
        var y = -document.body.clientHeight / 2 + 560 + "px"
        dom.style.transition = "0.5s"
        dom.style.transform = "translateX(" + x + ") translateY(" + y + ") scale(0)"
        setTimeout(function() {
          dom2.style.transform = "scale(1)"
        }, 500)

      },

      setMock:function(){
        axios.post("http://127.0.0.1:3000/test",{
          "id":123
        })
        .then(res => {
          console.log(res)
        })
      },

      unshow: function() {
        var dom = this.$refs.bar
        var dom2 = this.$refs.window
        dom2.style.transition = "0.5s"
        dom2.style.transform = "scale(0)"
        dom2.style.top = "50%";
        dom2.style.left = "50%";
        setTimeout(function() {
          dom.style.transform = "translateX(0) translateY(0) scale(1)"
        }, 500)
      }
    }
  }
</script>
<style>
.bottom_bar {
    width: 60px;
    height: 60px;
    border-radius: 45px;
    background-color: #0070CC;
    position: fixed;
    bottom: 50px;
    right: 50px;
    cursor: pointer;
  }

  .window {
    width: 900px;
    height: 600px;
    left: 50%;
    top: 50%;
    margin-top: -300px;
    margin-left: -450px;
    transform: scale(0);
    transition: 0.5s;
    position: fixed;
    border: 1px solid #0070CC;
    box-sizing: border-box;
    z-index: 99999;
  }

  .bottom_bar:hover {
    box-shadow: 0 0 10px #0070CC;
  }

  .unshow {
    float: right;
    height: 100%;
    width: 40px;
    text-align: center;
    cursor: pointer;
  }

  .unshow:hover {
    background-color: #0070CC;
  }

  .cmdBody {
    height: 570px;
    background-color: white;
    font-weight: 200;
    font-size: 12px;
    padding: 10px;
  }
</style>
