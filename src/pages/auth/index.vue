<template>
  <view>
    <!-- 点击登录 1、调用微信的方法 获取微信账号信息 用获取到的账号去登陆 登录成功之后 本地存入 toke -->
    <button type="primary" open-type="getUserInfo" @getuserinfo="getUser">微信登录</button>
  </view>
</template>

<script>
  export default {
    data(){
      return {

      }
    },
    methods:{
      // 点击按钮 获取微信个人信息
      getUser(user){
        console.log(user);
        if (user.detail.errMsg === "getUserInfo:fail auth deny")  {
          wx.showToast({title:'您已拒绝 授权信息'})
          return
        }
      },
    }
  }
</script>

<style lang="scss" scoped>
  button {
    width: 600rpx;
    margin: 200rpx auto 0;
  }
</style>