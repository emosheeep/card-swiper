<template>
  <div class="card__swiper" ref="swiper">
    <!-- 图片容器 -->
    <div
      class="img__container"
      ref="container"
    >
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

    <!-- 左右切换按钮 -->
    <div
      :class="['pre', { 'pre__active': isEnter }]"
      @click="pre"
    >
      <slot name="pre">
        <div class="icon__pre">
          <img src="../assets/left.svg">
        </div>
      </slot>
    </div>
    <div
      :class="['next', { 'next__active': isEnter }]"
      @click="next"
    >
      <slot name="next">
        <div class="icon__next">
          <img src="../assets/right.svg">
        </div>
      </slot>
    </div>

    <!-- 导航栏 -->
    <ul class="card-nav">
      <li
        v-for="(item, index) in images"
        :key="index"
        @click="setIndex(index, item)"
        @mouseover="setIndex(index, item)"
      >
        <!-- li元素用来占位，增加面积，span元素用来显示 -->
        <span
          :class="['card-nav-item', {
            active: index === idx - 1
          }]"
        />
      </li>
    </ul>
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
      default: 500
    },
    height: {
      type: [Number, String],
      default: 250
    },
    autoplay: {
      type: [Boolean, Number],
      default: 3000
    }
  },
  data () {
    return {
      idx: 1,
      isComplete: true, // 控制事件切换频率
      timer: null, // 控制轮播定时器
      isEnter: false // 控制按钮动画
    }
  },
  computed: {
    index: {
      set (newVal) {
        if (newVal === this.idx) {
          return
        }
        if (newVal === 0) {
          this.move(newVal, el => {
            this.idx = this.images.length
            el.style.left = `${this.right}px`
          })
        } else if (newVal === this.images.length + 1) {
          // 跳转到起点
          const distance = newVal * this.baseWidth
          this.move(distance, el => {
            this.idx = 1
            el.style.left = `${this.baseWidth}px`
          })
        } else {
          this.idx = newVal
          this.move(this.baseWidth * this.idx)
        }
        this.setNavItem(newVal, this.idx)
        this.setTimer() // 重置定时器，提升体验
      },
      get () {
        return this.idx
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
    // 设置下方导航条
    setNavItem (newVal, oldVal) {
      // 转换两端的索引
      if (newVal === this.images.length + 1) {
        newVal = newVal - this.images.length
      } else if (newVal === 0) {
        newVal = this.images.length
      }
      this.$emit('change', newVal - 1, this.idx - 1)
    },
    setTimer () {
      if (!this.autoplay) {
        return
      }
      let time = 3000
      if (typeof this.autoplay === 'number' && this.autoplay > time) {
        time = this.autoplay
      }
      // 重载
      this.setTimer = () => {
        if (this.timer) {
          clearInterval(this.timer)
        }
        this.timer = setInterval(this.next.bind(this), time)
      }
      this.setTimer()
    },
    bindHover () {
      const enterHandler = e => {
        this.isEnter = true
      }
      const leaveHandler = e => {
        this.isEnter = false
      }
      this.$refs.swiper.addEventListener('mouseenter', enterHandler)
      this.$refs.swiper.addEventListener('mouseleave', leaveHandler)
      this.$once('hook:beforeDestroy', () => {
        this.$refs.swiper.removeEventListener('mouseenter', enterHandler)
        this.$refs.swiper.removeEventListener('mouseleave', leaveHandler)
      })
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
    if (this.images.length === 0) {
      throw new Error('images shouldn\'t be empty !')
    }
    this.init()
    this.setTimer()
    this.bindHover()
  }
}
</script>

<style scoped lang="stylus">
// 轮播样式
.card__swiper
  position relative
  overflow hidden
.img__container
  position absolute
  display flex
  flex-wrap nowrap
// 前后按钮
.pre, .next
  position absolute
  top 50%
  transition all .5s ease
  transform translateY(-50%)
  cursor pointer
  user-select none
.pre
  left -40px
.pre__active
  left 10px
.next
  right -40px
.next__active
  right 10px
// 按钮图标
.icon__next, .icon__pre
  display flex
  justify-content center
  align-items center
  width 40px
  height 40px
  border-radius 40px
  background-color rgba(0, 0, 0, .3)
  &:hover
    background-color rgba(0, 0, 0, .2)
  img
    width 20px
    height 20px
// 导航样式
ul.card-nav
  position absolute
  bottom 0
  width 100%
  height 20px
  padding 0
  margin 0
  list-style none
  li
    display inline
    margin 0 5px
    cursor pointer
  .card-nav-item
    display inline-block
    vertical-align middle
    width 20px
    height 2px
    background-color black
    opacity 0.4
    transition all .5s ease
.active
  background-color white !important
  opacity 1 !important
</style>
