<template>
  <div class='comment-list'>
     <div class="page-title" v-if="comments.length">
         我的评论
     </div>
     <div class="comment"
        v-for="comment in comments"
        :key="comment.id"
        @click="handleClick(comment)"
     >
       <div class="user">
           <div class="inline">
               <img :src="comment.image"
                     class="avatar"
                     mode='aspectFit'     
               >
               {{comment.title}}
           </div>
       </div>
       <div class="right">
          {{comment.location || '未知地点'}}
          <span class="text-primary">
              --
          </span>
          {{comment.phone || '未知型号'}}
       </div>
       <div class="content">
           {{comment.comment}}
       </div>
     </div>
  </div>    
</template>
<script>
export default {
  props: ['comments', 'type'],
  methods: {
      handleClick(comment) {
          if(this.type === 'user') {
               wx.navigateTo({
                   url:'/pages/detail/main?id='+comment.bookid
               })
          }
      }
  }
};
</script>
<style lang="stylus" scoped>
  .comment-list
     background: #eee;
     font-size: 14px;
     width: 100%
     .comment
       background: #fff;
       margin-bottom: 10px;
       padding: 5px 20px;
       .content
         margin: 10px 0;
       .user
         display: inline
         .inline
           display: inline
           .avatar
              width: 20px
              height: 20px
              border-radius: 50%
</style>
