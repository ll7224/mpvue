<template>
  <a :href="detailUrl">
    <div class="book-card">
      <div class="thumb" @click.stop="preview">
          <img :src='book.image' class="image" model="scaleToFill">
      </div>
      <div class="detail">
          <div class="row text-primary">
              <div class="right">{{book.rate}} <Rate :value='book.rate'></Rate></div>
              <div class="left">{{book.title}}</div>
          </div>
          <div class="row">
              <div class="right text-primary">浏览量:{{book.count}}</div>
              <div class="left">{{book.author}}</div>
          </div>
          <div class="row">
              <div class="right">{{book.user_info.nickName}}</div>
              <div class="left">{{book.publisher}}</div>
          </div>
      </div>
    </div>
  </a>
</template>
<script>
import Rate from '@/components/Rate';

export default {
  props: ['book'],
  components: {
    Rate,
  },
  computed: {
    detailUrl() {
      return `/pages/detail/main?id=${this.book.id}`;
    },
  },
  methods: {
    preview() {
      wx.previewImage({
        current: this.book.image,
        urls: [this.book.image],
      });
    },
  },
};
</script>
<style lang="stylus" scoped>
  .book-card
    border: 1px solid #333
    border-radius: 5px
    pdding: 5px;
    overflow: hidden;
    margin: 5px 15px 15px 15px
    font-size 14px
    .thumb
      width: 90px;
      height: 90px;
      float: left;
      margin: 0 auto;
      overflow: hidden
      .image
         max-width: 100%
         max-height: 100%
    .detail
      margin-left: 100px
      .row
        line-height: 20px;
        margin-bottom: 3px
        .right
          float: right
          margin-right: 10px
        .left
          margin-right: 80px
</style>
