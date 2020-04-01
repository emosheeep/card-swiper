<template>
   <card-swipe
      :images="images"
      @change="onChange"
      class="swiper"
    />
</template>

<script>
export default {
  name: 'example',
  components: {
    'card-swipe': () => import('@/components/CardSwiper.vue')
  },
  data () {
    return {
      path: 'http://file.biubiubius.com/background',
      images: []
    }
  },
  methods: {
    onChange (index, oldIndex) {
      console.log(`curIndex：${index}`, `oldIndex：${oldIndex}`)
    }
  },
  mounted () {
    const img = [
      '对弈.jpeg', '古风男子.jpg', '我的一个道姑朋友.png', '断桥.png',
      '昨日青空.jpg', '李白.jpg', '独占我的英雄.jpg', '花丛.jpg', '魔道祖师.jpg'
    ]
    this.images = img.map(item => `${this.path}/${item}`)
  }
}
</script>

<style scoped lang="stylus">
.swiper
  margin 0 auto
</style>
