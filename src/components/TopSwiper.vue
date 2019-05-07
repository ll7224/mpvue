<template>
  <div class="swiper">
     <swiper
       :indicator-dots="true"
       indicator-color="#EA5149"
       :autoplay="true"
       :interva='6000'
       :duration="1000"
       :circular='true'
     >
       <div :key="imgeIndex" v-for="(top,imgeIndex) in imgUrls">
           <swiper-item>
               <img class="slider-image" 
               mode='aspectFit' 
               v-for="img in top"
               :src="img.image" 
               :key="img.id"
               alt=""
               @click="getDetail(img)"
               >
           </swiper-item>
       </div>
     </swiper>
  </div>
</template>
<script>
export default {
  props: ['tops'],
  computed: {
    imgUrls() {
      // 如果通用 请用chunk函数 比如lodash的chunk函数
      const res = this.tops;
      return [res.slice(0, 3), res.slice(3, 6), res.slice(6)];
    },
  },
  methods: {
    getDetail(item) {
      wx.navigateTo({
        url: `/pages/detail/main?id=${item.id}`,
      });
    },
  },
};
</script>
<style lang="stylus" scoped>
  .swiper
     margin-top: 5px
     .slider-image
       width: 33%;
       height: 250rpx
</style>
