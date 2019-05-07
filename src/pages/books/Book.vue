<template>
  <div class="book-wrapper">
      <TopSwiper :tops='tops'></TopSwiper>
      <cardshow :key='book.id' v-for="book in books" :book="book"></cardshow>
      <p class="text-footer" v-if="!more">
        没有更多书籍
      </p>
  </div>    
</template>
<script>
import { get } from '@/util';
import cardshow from '@/components/cardshow';
import TopSwiper from '@/components/TopSwiper';

export default {
  data() {
    return {
      books: [],
      page: 0,
      more: true,
      tops: [],
    };
  },
  methods: {
    async getList(init) {
      if (init) {
        this.page = 0;
        this.more = true;
      }
      wx.showNavigationBarLoading();
      const book = await get('/weapp/booklist', {
        page: this.page,
      });
      if (book.list.length < 10 && this.page > 0) {
        this.more = false;
      }
      if (init) {
        this.books = book.list;
        wx.stopPullDownRefresh();
      } else {
        // 下拉刷新，不能直接覆盖books，而是累加
        this.books = this.books.concat(book.list);
      }
      wx.hideNavigationBarLoading();
    },
    async getTop() {
      const tops = await get('/weapp/top');
      this.tops = tops.list;
    },
  },
  onPullDownRefresh() {
    this.getList(true);
    this.getTop();
    //
  },
  onReachBottom() {
    if (!this.more) {
      // 没有更多
      return false;
    }
    this.page = this.page + 1;
    this.getList();
    return '';
  },
  mounted() {
    this.getList(true);
    this.getTop();
  },
  components: {
    cardshow, TopSwiper,
  },
};
</script>
<style lang="stylus" scoped>

</style>

