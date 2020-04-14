<template>
  <!-- 搜索 -->
  <div class="search" :class="{active: isfocus}">
    <!-- 搜索框 -->
    <div class="input-wrap" @click="goSearch">
      <!-- 监听文本框 输入关键字 -->
      <!-- 绑定输入值 -->
      <input type="text" placeholder="请输入搜索商品" v-model="keyword" @input="query" @confirm="goList" />
      <span class="cancle" @click.stop="goCancel">取消</span>
    </div>
    <!-- 搜索结果 -->
    <div class="search-content">
      <!-- 点击 × 清空搜索历史 -->
      <div class="title">搜索历史<span class="clear" @click="remove"></span></div>
      <div class="history">
        <navigator url="/pages/list/index" v-for="item in history" :key="item">{{ item }}</navigator>
      </div>
      <!-- 结果 -->
      <!-- 有了搜索结果 才有必要显示 这个列表 -->
      <scroll-view scroll-y class="result" v-if="list.length>0">
        <navigator url="/pages/goods/index" v-for="item in list" :key="item.goods_id">{{ item.goods_name }}</navigator>
      </scroll-view>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        isfocus: false,
        placeholder: '',
        keyword:'' ,// 搜索文本框的 输入值
        list:[] ,// 搜索结果
        history:wx.getStorageSync("histry")||[] // 搜索记录储存
      }
    },
    methods: {
      // 点击清空搜索记录
      remove(){
        this.history = []
         uni.setStorageSync("histry", this.history);
      },
      // 文本框火车 跳转到商品页面 带上参数传过去
      goList(){
        this.history.push(this.keyword)
        // console.log(this.history);
        // 去除重复 添加到本地缓存
        this.history = [...new Set(this.history)]
        uni.setStorageSync("histry", this.history);
        // 跳转列表 并带上参数
        if (this.keyword !== '') {
        uni.navigateTo({
          url: '/pages/list/index?query=' + this.keyword,
        });
       }
      },
      // 文本框 输入值触发事件 接收一个参数
      async query(){
        // console.log(this.keyword);
           let ser =await this.http({
          url:"/api/public/v1/goods/qsearch",
          data:{
            query:this.keyword
          }
        })
        this.list = ser.message
      },
      goSearch (ev) {
        // 获取焦点 isfocus true 加上 active
        this.isfocus=true;
        // 获取屏幕高度 微信提供了
        // wx. ---》换成 uni. 就行
        let res= uni.getSystemInfoSync()
        console.log('结果',res)
        this.$emit("my",res.windowHeight+'px')

        // 隐藏tabBar
        uni.hideTabBar();
      },
      goCancel () {
        // 取消 isfocus false 加上 active
        this.isfocus=false;

        // 触发父组件自定义事件
        this.$emit('my', 'auto');

        // 显示tabBar
        uni.showTabBar();
        this.keyword = ''
      }
    }
  }
</script>

<style lang="scss" scoped>
  .search {
    display: flex;
    flex-direction: column;
    
    // 搜索框
    .input-wrap {
      display: flex;
      height: 100rpx;
      padding: 20rpx 30rpx;
      background-color: #ea4451;
      box-sizing: border-box;
      position: relative;

      // &::before,
      // &::after {
      //   height: 44rpx;
      //   line-height: 1;
      //   background-image: url(http://static.botue.com/ugo/images/icon_search%402x.png);
      //   background-size: 32rpx;
      //   background-position: 6rpx center;
      //   background-repeat: no-repeat;

      //   position: absolute;
      //   top: 28rpx;
      //   z-index: 9;
      // }

      // &::before {
      //   content: '搜索';
      //   display: block;

      //   width: 100rpx;
      //   padding: 11rpx 0 10rpx 44rpx;
      //   box-sizing: border-box;
      //   color: #666;
      //   font-size: 24rpx;
      //   left: 325rpx;
      // }

      // &::after {
      //   display: none;
      //   content: '';
      //   width: 44rpx;
      //   left: 40rpx;
      // }

      input {
        flex: 1;
        height: 60rpx;
        padding: 0 20rpx 0 64rpx;
        background-color: #fff;
        border-radius: 8rpx;
        font-size: 24rpx;
        color: #666;
      }

      span.cancle {
        display: none;
        width: 80rpx;
        text-align: right;
        line-height: 60rpx;
        font-size: 27rpx;
        color: #666;
      }
    }

    // 搜索结果
    .search-content {
      display: none;
      flex: 1;
      padding: 27rpx;
      background-color: #fff;
      position: relative;

      .title {
        font-size: 27rpx;
        line-height: 1;
        color: #333;
      }

      .clear {
        display: block;
        width: 27rpx;
        height: 27rpx;
        float: right;
        background-image: url(http://static.botue.com/ugo/images/clear.png);
        background-size: cover;
      }

      .history {
        padding-top: 30rpx;

        navigator {
          display: inline-block;
          line-height: 1;
          padding: 15rpx 20rpx 12rpx;
          background-color: #ddd;
          font-size: 24rpx;
          margin-right: 20rpx;
          margin-bottom: 15rpx;
          color: #333;
        }
      }

      .result {
        // display: none;
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        background-color: #fff;

        navigator {
          line-height: 1;
          padding: 20rpx 30rpx;
          font-size: 24rpx;
          color: #666;
          border-bottom: 1px solid #eee;

          &:last-child {
            border-bottom: none;
          }
        }
      }
    }
    
    // 获得焦点状态
    &.active {
      width: 100%;
      height: 100%;
      position: absolute;
      left: 0;
      top: 0;
      z-index: 9;

      .input-wrap {
        background-color: #eee;

       
      }

      span.cancle {
        display: block;
      }

      .search-content {
        display: block;
      }
    }
  }
</style>