<template>
   <div>
     <card-swipe
       :images="images"
       width="300"
       height="150"
       @change="onChange"
     />
   </div>
</template>

<script>
export default {
  name: 'example',
  components: {
    'card-swipe': () => import('@/components/CardSwiper.vue')
  },
  data () {
    return {
      images: []
    }
  },
  methods: {
    onChange (index, oldIndex) {
      console.log(index, oldIndex)
    }
  },
  created () {
    const img = [
      '古风男子.jpg', '对弈.jpeg', '我的一个道姑朋友.png', '断桥.png',
      '昨日青空.jpg', '李白.jpg', '独占我的英雄.jpg', '花丛.jpg', '魔道祖师.jpg'
    ]
    this.images = img.map(item => require(`@/assets/images/${item}`))
  }
}
</script>

<style scoped lang="stylus">

</style>
