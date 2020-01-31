<template>
    <div class="card-swiper" ref="swiper">
      <div id="img-container" ref="container">
        <img
          alt="辅助图"
          :src="images[images.length - 1]"
        />
        <img
          v-for="(src, index) in images"
          :key="index"
          :src="src"
          :alt="`第${ index + 1 }张`"
        />
        <img
          alt="辅助图"
          :src="images[0]"
        />
      </div>
      <div class="ctrl-btn">
<!--        fontawesome-->
        <i class="fa fa-angle-left pre" @click="pre"/>
        <i class="fa fa-angle-right next" @click="next"/>
      </div>
      <div class="card-nav" ref="nav">
        <div
          class="card-nav-item"
          v-for="(item, index) in images"
          :key="index"
          @click="setIndex(index, item)"
          @mouseover="setIndex(index, item)"
        />
      </div>
    </div>
</template>

<script>
import Velocity from 'velocity-animate'
export default {
  name: 'CardSwiper',
  props: {
    images: {
      type: Array,
      required: true
    },
    width: [Number, String],
    height: [Number, String]
  },
  data () {
    return {
      tempIndex: 1,
      isComplete: true
    }
  },
  computed: {
    index: {
      set (newVal) {
        if (newVal === this.tempIndex) {
          return
        }
        this.setNavItem(newVal, this.tempIndex) // 传入新旧值
        if (newVal === 0) {
          this.move(0, el => {
            this.tempIndex = this.images.length
            el.style.left = `${this.right}px`
          })
        } else if (newVal === this.images.length + 1) {
          this.move(-3000, el => {
            this.tempIndex = 1
            el.style.left = `${this.baseWidth}px`
          })
        } else {
          this.tempIndex = newVal
          this.move(this.baseWidth * this.tempIndex)
        }
      },
      get () {
        return this.tempIndex
      }
    },
    baseWidth () {
      return -this.$refs.container.offsetWidth / (this.images.length + 2)
    },
    right () {
      return this.baseWidth * this.images.length
    }
  },
  methods: {
    pre () {
      if (this.isComplete) {
        this.index--
      }
    },
    next () {
      if (this.isComplete) {
        this.index++
      }
    },
    setIndex (index) {
      if (this.isComplete) {
        this.index = index + 1
      }
    },
    // 轮播动画
    move (distance, callback = null) {
      this.isComplete = false
      Velocity(this.$refs.container, {
        left: distance
      }, {
        complete: ([el]) => {
          this.isComplete = true
          callback && callback(el)
        }
      })
    },
    // 设置下方导航球
    setNavItem (newVal, oldVal) {
      const navItems = this.$refs.nav.children
      // 转换两端的索引
      if (newVal === this.images.length + 1) {
        newVal = newVal - this.images.length
      } else if (newVal === 0) {
        newVal = this.images.length
      }
      this.$emit('change', newVal - 1, this.tempIndex - 1)
      Object.assign(navItems[newVal - 1].style, {
        backgroundColor: 'orangered',
        opacity: 1
      })
      Object.assign(navItems[oldVal - 1].style, {
        backgroundColor: 'black',
        opacity: 0.5
      })
    },
    init () {
      const images = document.querySelectorAll('.images')
      images.forEach(img => {
        img.style.width = `${this.width}px`
        img.style.height = `${this.height}px`
      })
      this.$refs.swiper.style.width = `-${this.width}px`
      this.$refs.swiper.style.height = `${this.height}px`
      this.$refs.container.style.left = `${this.baseWidth}px`
    }
  },
  mounted () {
    this.init()
    if (this.$attrs.hasOwnProperty('autoplay')) {
      const time = this.$attrs.autoplay || 3000
      setInterval(this.next.bind(this), time)
    }
  }
}
</script>

<style scoped lang="stylus">
.card-swiper
  $width = 300px
  position relative
  width $width
  height 150px
  margin 0 auto
  overflow hidden
  #img-container
    display flex
    flex-wrap nowrap
    position absolute
    left 0
    height 100%
    img
      width $width
      height 100%
.card-nav
  position absolute
  bottom 0
  left 50%
  transform translateX(-50%)
  display flex
  flex-wrap nowrap
  justify-content space-around
  width 50%
  height 20px
  .card-nav-item
    width 10px
    height 10px
    margin auto 0
    background-color black
    opacity 0.5
    border-radius 10px
    &:nth-child(1)
      background-color orangered
      opacity 1
.ctrl-btn
  position relative
  top 50%
  transform translateY(-50%)
  height 40px
  margin auto 0
  font-size 40px
  opacity 0.8
  .pre
    position absolute
    left 0
  .next
    position absolute
    right 0
</style>
