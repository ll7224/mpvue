<template>
  <div class="login-wrapper">
      <div class="userInfo">
        <img :src="userInfo.avatarUrl" alt="">
        <p>{{userInfo.nickName}}</p>
        <button class="loginBtn" open-type="getUserInfo" @getuserinfo="bindGetUserInfo" @click="getUserInfo1" v-show="showBtn">点击登录</button> 
      </div>
      <YearProgress></YearProgress>
      <button @click="scanBook" class='btn' v-if='!showBtn'>添加图书</button>
  </div>
</template>
<script>
import qcloud from 'wafer2-client-sdk';
import YearProgress from '@/components/YearProgress';
import config from '../../config';
import { showSuccess, post, showModal } from '../../util';


export default {
  data() {
    return {
      showBtn: true,
      userInfo: {
        avatarUrl: '../../../static/img/unlogin.png',
      },
    };
  },
  created() {
    // wx.setNavigationBarTitle({
    //   title: '个人中心',
    // });
    this.getSetting();
  },
  components: {
    YearProgress,
  },
  methods: {
    getSetting() {
      qcloud.setLoginUrl(config.loginUrl);
      const session = qcloud.Session.get();
      if (session) {
        // 第二次登录
        // 或者本地已经有登录态
        // 可使用本函数更新登录态
        qcloud.loginWithCode({
          success: (res) => {
            // showSuccess('登录成功')
            this.userInfo = res;
            wx.setStorageSync('userinfo', res);
            this.showBtn = false;
          },
          fail: (err) => {
            // console.error('222', err);
            showModal('登录失败', err);
          },
        });
      } else {
        // 首次登录
        qcloud.login({
          success: (res) => {
            this.userInfo = res;
            showSuccess('登录成功');
            // this.setData({ userInfo: res, logged: true })
            wx.setStorageSync('userinfo', res);
            this.showBtn = false;
          },
          fail: (err) => {
            // console.log(err);
            showModal('登录失败', err);
          },
        });
      }
    },
    getUserInfo1() {
      // console.log('click事件首先触发');
      // 判断小程序的API，回调，参数，组件等是否在当前版本可用。  为false 提醒用户升级微信版本
      // console.log(wx.canIUse('button.open-type.getUserInfo'))
      if (wx.canIUse('button.open-type.getUserInfo')) {
        // 用户版本可用
      } else {
        // console.log('请升级微信版本');
      }
    },
    bindGetUserInfo(e) {
      // console.log(e.mp.detail.rawData)
      if (e.mp.detail.rawData) {
        // 用户按了允许授权按钮
        // console.log('用户按了允许授权按钮');
        showSuccess('登录成功');
        this.userInfo = e.mp.detail.userInfo;
        wx.setStorageSync('userinfo', e.mp.detail.userInfo);
        this.showBtn = false;
        //  wx.switchTab({
        //       url:"/pages/me/main"
        //     })
      } else {
        // 用户按了拒绝按钮
        // console.log('用户按了拒绝按钮');
      }
    },
    async addBook(isbn) {
      const res = await post('/weapp/addbook', {
        isbn,
        openid: this.userInfo.openId,
      });
      showModal('添加成功', `${res.title}添加成功`);
      // if(res.code === 0 && res.data.title) {
      //   showSuccess('添加成功', `${res.data.title}添加成功`)
      // }
    },
    scanBook() {
      wx.scanCode({
        success: (res) => {
          if (res.result) {
            this.addBook(res.result);
          }
        },
      });
    },

  },

};
</script>
<style lang="stylus" scoped>
  .login-wrapper
    position:relative;
    width: 100%;
    .loginBtn
      width: 40%
      font-size: 14px
    .userInfo
      padding: 0 30rpx
      text-align: center
      img 
        width: 150rpx
        height: 150rpx
        margin: 20rpx
        border-radius: 50%
    .btn
      color: #fff;
      background: #EA5A49;
      margin-top: 10px;
      margin-bottom: 10px;
      padding-left: 15px;
      border-radius: 2px;
      font-size: 16px;
      line-height: 40px;
      height: 40px;
      width: 50%
    .btn:active
      background: #FA5A49
</style>

