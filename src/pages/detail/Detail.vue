<template>
   <div>
    <!-- <div>
        图书id:{{bookid}}
    </div> -->
    <BookInfo :info='info'></BookInfo>
    <CommentsList :comments='comments'></CommentsList>
    <div class="comments" v-if="showAdd">
         <textarea v-model='comment'
                   class="textarea"
                   :maxlength="100"
                   placeholder="请输入图书短评"
                   >
         </textarea>
         <div class="location">
             地理位置:
             <switch color='#EA5A49' :checked='location' @change="getGeo"></switch>
             <span class="text-primary">{{location}}</span>
         </div>
         <div class="phone">
             手机型号:
             <switch color='#EA5A49' :checked='phone' @change="getPhone"></switch>
             <span class="text-primary">{{phone}}</span>
         </div>
         <button class="btn" @click="addComment">评论</button>
    </div>
    <div v-else class="text-footer">
        未登录或者已经评论过了
    </div>
    <button class="btn" open-type="share">转发给好友</button>
   </div>
</template>
<script>
import { get, post, showModal } from '@/util';
import BookInfo from '@/components/BookInfo';
import CommentsList from '@/components/CommentsList';

export default {
  data() {
    return {
      userinfo: '',
      bookid: '',
      info: {},
      phone: '',
      comment: '',
      location: '',
      comments: [],
    };
  },
  components: {
    BookInfo,
    CommentsList,
  },
  computed: {
    showAdd() {
      // 没登录
      if (!this.userinfo) {
        return false;
      }
      // 评论页面里查到有自己的openid
      if (this.comments.filter(v => v.openid === this.userinfo.openId).length) {
        return false;
      }
      return true;
    },
  },
  methods: {
    async getDetail() {
      const info = await get('/weapp/bookdetail', { id: this.bookid });
      wx.setNavigationBarTitle({
        title: info.title,
      });
      this.info = info;
    },
    async getComment() {
      const comments = await get('/weapp/commentlist', {
        bookid: this.bookid,
      });
      this.comments = comments.list || [];
    },
    getPhone(e) {
      if (e.target.value) {
        const phoneInfo = wx.getSystemInfoSync();
        this.phone = phoneInfo.model;
      } else {
        this.phone = '';
      }
    },
    getGeo(e) {
      const ak = 'rTLiaGBNG8on66SwRZb7CgL4eQ7iwTsa';
      const url = 'http://api.map.baidu.com/geocoder/v2/';
      if (e.target.value) {
        wx.getLocation({
          success: (geo) => {
            wx.request({
              url,
              data: {
                location: `${geo.latitude},${geo.longitude}`,
                output: 'json',
                ak,
              },
              success: (res) => {
                if (res.data.status === 0) {
                  this.location = res.data.result.addressComponent.city;
                } else {
                  this.location = '未知地点';
                }
              },
            });
          },
        });
      } else {
        this.location = '';
      }
    },
    async addComment() {
      if (!this.comment) {
        showModal('无法提交', '请输入评论');
        return;
      }
      const data = {
        openid: this.userinfo.openId,
        bookid: this.bookid,
        comment: this.comment,
        phone: this.phone,
        location: this.location,
      };
      // 评论内容， 手机型号，地理位置，图书id，用户的openid
      try {
        const res = await post('/weapp/addComment', data);
        showModal('提交成功', res.msg);
        this.comment = '';
        this.getComment();
      } catch (e) {
        showModal('失败', e.msg);
      }
    },
  },
  mounted() {
    this.bookid = this.$root.$mp.query.id;
    this.getDetail();
    this.getComment();
    const userinfo = wx.getStorageSync('userinfo');
    if (userinfo) {
      this.userinfo = userinfo;
    }
  },
};
</script>
<style lang="stylus" scoped>
 .comments
    margin-top: 10px
    .textarea
       width: 730rpx;
       height: 200rpx
       background: #eee;
       padding: 10px
    .location
       margin-top: 10px;
       fons-size: 14px
       padding: 5px
    .phone
       margin-top: 10px
    .btn
       margin-top: 10px
       margin-bottom : 10px
       background: #ea5a49
       color: #ffffff 
       width: 70%
</style>
