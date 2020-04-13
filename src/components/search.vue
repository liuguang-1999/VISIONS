<template>
  <!-- 搜索 -->
  <div class="search" :class="{active: isfocus}">
    <!-- 搜索框 -->
    <div class="input-wrap" @click="goSearch">
      <input type="text" placeholder="请输入搜索商品">
      <span class="cancle" @click.stop="goCancel">取消</span>
    </div>
    <!-- 搜索结果 -->
    <div class="search-content">
      <div class="title">搜索历史<span class="clear"></span></div>
      <div class="history">
        <navigator url="/pages/list/index">小米</navigator>
        <navigator url="/pages/list/index">智能电视</navigator>
        <navigator url="/pages/list/index">小米空气净化器</navigator>
        <navigator url="/pages/list/index">西门子洗碗机</navigator>
        <navigator url="/pages/list/index">华为手机</navigator>
        <navigator url="/pages/list/index">苹果</navigator>
        <navigator url="/pages/list/index">锤子</navigator>
      </div>
      <!-- 结果 -->
      <scroll-view scroll-y class="result">
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
        <navigator url="/pages/goods/index">小米</navigator>
      </scroll-view>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        isfocus: false,
        placeholder: ''
      }
    },
    methods: {
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