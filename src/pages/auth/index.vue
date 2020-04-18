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
      // 1、 带调用微信 特有的方法 获取用户的个人账号信息
      // 点击按钮 获取微信个人信息
      async getUser(user){
        console.log(user);
        if (user.detail.errMsg === "getUserInfo:fail auth deny")  {
          wx.showToast({title:'您已拒绝 授权信息'})
          return
        }
        // 2、 用 用户的账号去 登录~~~~还需要 微信的登录后的code 识别码  小程序登录都需要的(识别码) 可以使用 wx.login() 方法去获取
        let ser =await uni.login()
        console.log("微信的code",ser); // code 设备登登录码 ser[1].code
          let loginser =await this.http({
              url:'/api/public/v1/users/wxlogin',
              method:'POST',
              data:{
                code:ser[1].code ,// 请求体需要 登录码 才能换取到 token
                encryptedData:user.mp.detail.encryptedData, // 以下都是 获取授权 后获得的参数
                iv:user.detail.iv,
                rawData:user.detail.rawData,
                signature:user.detail.signature
            }
          })
          console.log("登陆后的请求参数",loginser);
        // 3、 登录之后 在本地存入 token
        if (loginser.meta.status === 200) {
          // 存 token 
          uni.setStorageSync('token',loginser.message.token)
          // 跳转回去登陆页面
          uni.navigateBack() // 返回上一页
        }
      }
      
      
    }
  }
</script>

<style lang="scss" scoped>
  button {
    width: 600rpx;
    margin: 200rpx auto 0;
  }
</style>