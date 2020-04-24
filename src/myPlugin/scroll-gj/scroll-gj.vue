<template>
  <div @scroll="pullUp" class="scroll" @touchstart="moveStart" :style="'padding-top: '+padding+'px'" ref="scroll">
    <div class="refresh" v-show="padding >= 40" :style="'line-height: '+ padding +'px'">松开立即刷新</div>
    <div class="scroll-content" :style="'padding: '+innerpad+'px; box-sizing: border-box;'">
      <slot/>
    </div>
    <img @click="gotop" v-show="showtop && backTop" class="backtop" src="http://zjdxcx.wiseljz.com/fe-images/top_ionic.png" alt="">
    <div class="empty" v-show="listInfo && listInfo.finish && listInfo.total === 0">
      <img class="empty-img" src="http://zjdxcx.wiseljz.com/fe-images/loading-empty.png" alt="">
      <div class="empty-div">数据为空</div>
    </div>
    <div class="nomore" v-show="listInfo && listInfo.finish && listInfo.total > 0 && listInfo.total === listInfo.listlength">----------没有更多啦！----------</div>
    <div id="preloader_1" v-show="listInfo && !listInfo.finish">
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <!-- <i>玩命加载中...</i> -->
    </div>
  </div>
</template>

<script>
export default {
  name: 'scrollGj',
  props: {
    listInfo: {
      type: Object,
      default: null
    },
    refresh: {
      type: Boolean,
      default: false
    },
    loadmore: {
      type: Boolean,
      default: true
    },
    innerpad: {
      type: Number,
      default: 0
    },
    backTop: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      timer: null, // 节流
      timer1: null, // 缓动动画
      timer2: null, // 缓动动画 回到顶部
      startY: 0,
      moveY: 0,
      padding: 0,
      showtop: false,
      scrollTop: 0
    }
  },

  created () {
  },
  mounted () {
  },
  methods: {
    // 回到顶部
    gotop () {
      clearInterval(this.timer2)
      this.timer2 = setInterval(() => {
        let sctop = this.getScrollTop()
        const step = Math.floor((0 - sctop) / 10)
        if (step >= 0) clearInterval(this.timer2)
        sctop += step
        this.$refs.scroll.scrollTop = sctop
      }, 15)

    },
    // 获取内容高度
    getScrollHeight () {
      return this.$refs.scroll.scrollHeight
    },
    // 获滚出内容高度
    getScrollTop () {
      return this.$refs.scroll.scrollTop
    },
    //  获取盒子高度
    getBoxHeight () {
      return this.$refs.scroll.offsetHeight
    },
    /**
     * pullScrollUp  
     * 上拉触底
     * listInfo: {
     *  listlength: XX, // Number
     *  total: xx, // Number
     *  finish: xx // Boolean
     * }
     */
    pullScrollUp () {
      if (!this.loadmore) return
      if (this.getBoxHeight() + this.getScrollTop() + 10 < this.getScrollHeight()) return
      this.$emit('pullUp')
    },
    pullUp () {
      if (!this.showtop && this.getScrollTop() > 500) this.showtop = true
      if (this.showtop && this.getScrollTop() <= 500) this.showtop = false
      if (this.timer) return
      if (this.listInfo.listlength === this.listInfo.total) return
      this.timer = setTimeout(() => {
        this.pullScrollUp()
        this.timer = null
      }, 200)
    },
    /**
     * 下拉刷新
     */
    moveStart (e) {
      if (!this.refresh || this.getScrollTop() > 0) return
      this.$refs.scroll.removeEventListener('touchend', this.movend)
      this.startY = e.touches[0].pageY
      // if (this.startY > 600) return
      this.$refs.scroll.addEventListener('touchmove', this.moving)
      this.$refs.scroll.addEventListener('touchend', this.movend)
    },
    moving (e) {
      this.moveY = e.touches[0].pageY - this.startY
      if (this.moveY > 80 || this.moveY < 0) return
      this.padding = this.moveY
    },
    movend () {
      this.moveY > 40 && this.$emit('pulldown')
      this.moveY = 0
      this.$refs.scroll.removeEventListener('touchmove', this.moving)
      clearInterval(this.timer1)
      this.timer1 = setInterval(() => {
        const step = Math.floor((0 - this.padding) / 10)
        if (step >= 0) clearInterval(this.timer1)
        this.padding += step
      }, 15)
    }
  }
}
</script>

<style>
.scroll {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  overflow-y: scroll;
  background-color: #fff;
 
}
.scroll .nomore {
  text-align: center;
  color: #bbb;
  font-size: 14px;
}
.scroll .refresh {
  position: absolute;
  width: 100%;
  top: 0;
  text-align: center;
  left: 0;
  font-size: 12px;
  color: #666;
  line-height: 40px;
}
.scroll .empty {
  text-align: center;
  padding: 120px;
}
.scroll .empty .empty-img {
  width: 80px;
  height: 80px;
}
.scroll .empty .empty-div {
  line-height: 22px;
  font-size: 14px;
  color: #333;
}
.scroll .backtop {
  position: fixed;
  width: 56px;
  height: 56px;
  right: 12px;
  bottom: 80px;
}
#preloader_1{
  position: fixed;
  z-index: 999;
  top: 50%;
  left: 50%;
  height: 30px;
  width: 55px;
  transform: translate(-50%,-50%);
}
#preloader_1 span{
  display:block;
  bottom:0px;
  width: 9px;
  height: 5px;
  background:#9b59b6;
  position:absolute;
  animation: preloader_1 1.5s  infinite ease-in-out;
}  
#preloader_1 span:nth-child(2){
  left:11px;
  animation-delay: .2s;  
}
#preloader_1 span:nth-child(3){
  left:22px;
  animation-delay: .4s;
}
#preloader_1 span:nth-child(4){
  left:33px;
  animation-delay: .6s;
}
#preloader_1 span:nth-child(5){
  left:44px;
  animation-delay: .8s;
}
@keyframes preloader_1 {
  0% {height:5px;transform:translateY(0px);background:#9b59b6;}
  25% {height:30px;transform:translateY(15px);background:#3498db;}
  50% {height:5px;transform:translateY(0px);background:#9b59b6;}
  100% {height:5px;transform:translateY(0px);background:#9b59b6;}
}
</style>
