<template>
   <card-swipe
      :autoplay="4000"
      width="600"
      height="300"
      :images="images"
      @change="onChange"
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
  created () {
    const img = [
      '古风男子.jpg', '对弈.jpeg', '我的一个道姑朋友.png', '断桥.png',
      '昨日青空.jpg', '李白.jpg', '独占我的英雄.jpg', '花丛.jpg', '魔道祖师.jpg'
    ]
    this.images = img.map(item => `${this.path}/${item}`)
  }
}
</script>

<style scoped lang="stylus">
.swiper
  width 500px !important
</style>
