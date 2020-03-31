<template>
    <div class="card__swiper" ref="swiper">
      <div class="img__container" ref="container">
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
      <nav class="card-nav">
        <div
          class="card-nav-item"
          v-for="(item, index) in images"
          :key="index"
          ref="navItems"
          @click="setIndex(index, item)"
          @mouseover="setIndex(index, item)"
        />
      </nav>
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
    width: {
      type: [Number, String],
      default: 300
    },
    height: {
      type: [Number, String],
      default: 150
    },
    autoplay: {
      type: [Boolean, Number],
      default: false
    }
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
          this.move(newVal, el => {
            this.tempIndex = this.images.length
            el.style.left = `${this.right}px`
          })
        } else if (newVal === this.images.length + 1) {
          // 跳转到最开始
          const distance = newVal * this.baseWidth
          this.move(distance, el => {
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
      const navItems = this.$refs.navItems
      // 转换两端的索引
      if (newVal === this.images.length + 1) {
        newVal = newVal - this.images.length
      } else if (newVal === 0) {
        newVal = this.images.length
      }
      navItems[newVal - 1].classList.add('active')
      navItems[oldVal - 1].classList.remove('active')
      this.$emit('change', newVal - 1, this.tempIndex - 1)
    },
    init () {
      const images = this.$refs.container.querySelectorAll('img')
      const { swiper, container } = this.$refs
      Array.from(images).forEach(img => {
        img.style.width = `${this.width}px`
        img.style.height = `${this.height}px`
      })
      swiper.style.width = `${this.width}px`
      swiper.style.height = `${this.height}px`
      container.style.left = `${this.baseWidth}px`
    }
  },
  mounted () {
    this.init()
    if (this.autoplay || typeof this.autoplay === 'number') {
      const time = this.autoplay > 2000 ? this.autoplay : 2000
      setInterval(this.next.bind(this), time)
    }
    if (this.images.length === 0) {
      throw new Error('请传入非空图片路径数组')
    }
  }
}
</script>

<style scoped lang="stylus">
.active
  background-color orangered !important
  opacity 1 !important
// 轮播样式
.card__swiper
  position relative
  overflow hidden
.img__container
  position absolute
  display flex
  flex-wrap nowrap
// 导航样式
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
  opacity 0.4
  border-radius 10px
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
