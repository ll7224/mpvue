<template>
  <div class="commentWrapper">
      <CommentsList v-if='userinfo.openId' :comments='comments' type='user'></CommentsList>
      <div v-if="userinfo.openId">
         <div class="page-title">我的图书</div>
         <cardshow
           v-for="book in books"
           :key="book.id"
           :book='book'
         ></cardshow>
         <div v-if="!books.length">暂时还没添加过书,快去添加几本书把</div>
      </div>
  </div>    
</template>
<script>
import { get } from '@/util'
import CommentsList from '@/components/CommentsList';
import cardshow from '@/components/cardshow';

export default {
  data() {
    return {
      comments: [],
      userinfo: {},
      books: []
    }
  },
  components: {
      CommentsList,cardshow
  },
  methods: {
    init() {
      wx.showNavigationBarLoading()
      this.getComments()
      this.getBooks()
      wx.hideNavigationBarLoading()
    },
    async getComments() {
      const comments = await get('/weapp/commentlist', {
        openid: this.userinfo.openId
      })
      this.comments = comments.list
    },
    async getBooks() {
       const bookid = await get('/weapp/booklist',{
          openid: this.userinfo.openId
       })
       console.log(this.books)
       this.books = bookid.list
    }
  },
  onPullDownRefresh() {
    this.init()
    wx.stopPullDownRefresh()
  },
  onShow() {
     if(!this.userinfo.openId) {
       let userinfo = wx.getStorageSync('userinfo')
       if(userinfo) {
         this.userinfo = userinfo
         this.init()
       }
     }
  },
}
</script>
<style lang="stylus" scoped>
  .container
    width: 100%
</style>
