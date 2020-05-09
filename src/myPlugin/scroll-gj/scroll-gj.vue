<template>
  <div @scroll="pullUp" class="scroll" :class="{'unfull': !fullWind}"  @touchstart="moveStart" :style="'padding-top: '+padding+'px'" ref="scroll">
    <div class="refresh" v-show="moveY > 40 && padding > 40" :style="'line-height: '+ padding +'px'">松开立即刷新</div>
    <div class="refresh" v-show="moveY === 0 && padding > 40 && !over" :style="'line-height: '+ padding +'px'">正在刷新数据...</div>
    <div class="refresh" v-show="moveY === 0 && padding > 40 && over" :style="'line-height: '+ padding +'px'">数据刷新完成</div>
    <div class="scroll-content" :style="'padding: '+innerPad+'px; box-sizing: border-box;'">
      <slot/>
    </div>
    <!-- http://zjdxcx.wiseljz.com/fe-images -->
    <img @click="gotop" v-show="showtop && backTop" class="backtop" src="data:image/png;base64,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" alt="">
    <div class="empty" v-show="listInfo && listInfo.finish && listInfo.total === 0">
      <img class="empty-img" src="data:image/png;base64,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" alt="">
      <div class="empty-div">数据为空</div>
    </div>
    <div class="nomore" v-show="listInfo && listInfo.finish && listInfo.total > 0 && listInfo.total === listInfo.listlength">----------没有更多啦！----------</div>
    <div id="preloader_1" v-show="listInfo && !listInfo.finish && over">
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
    loadMore: {
      type: Boolean,
      default: true
    },
    innerPad: {
      type: Number,
      default: 0
    },
    backTop: {
      type: Boolean,
      default: false
    },
    fullWind: {
      type: Boolean,
      default: true
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
      scrollTop: 0,
      over: true
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
      if (!this.loadMore) return
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
      this.moveY = 0
      this.over = false
      this.$refs.scroll.removeEventListener('touchmove', this.moving)
      this.padding > 40 && this.$emit('pullDown', { close: this.closePadding })
      this.padding <=40 && this.closePadding(0)
    },
    closePadding (time=800) {
      this.over = true
      clearInterval(this.timer1)
      setTimeout(() => {
        this.timer1 = setInterval(() => {
          const step = Math.floor((0 - this.padding) / 10)
          if (step >= 0) clearInterval(this.timer1)
          this.padding += step
        }, 15)
      }, time)
    }
  }
}
</script>

<style>
.scroll {
  position: fixed;
  top: 0;
  /* bottom: 0; */
  height: 100%;
  left: 0;
  right: 0;
  overflow-y: scroll;
  background-color: #eee;
  box-sizing: border-box;
 
}
.scroll .unfull {
  position: static;
  width: 100%;
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
.scroll .scroll-content {
  background-color: #fff;
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
