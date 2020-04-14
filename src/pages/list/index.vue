<template>
  <view class="list">
    <!-- 筛选 -->
    <view class="filter">
      <text class="active">综合</text>
      <text>销量</text>
      <text>价格</text>
    </view>
    <!-- 商品列表 -->
    <view class="goods">
      <!-- 一个商品 -->
      <view class="item" @click="goDetail(item.goods_id)" v-for="item in goodslist" :key="item.goods_id">
        <!-- 商品图片 -->
        <!-- <image class="pic" :src="item.goods_small_logo"></image> -->
        <image class="pic" :src="item.goods_small_logo" alt="" />
        <!-- 商品信息 -->
        <view class="meta">
          <view class="name">{{item.goods_name}}</view>
          <view class="price">
            <text>￥</text>{{item.goods_price}}<text>.00</text>
          </view>
        </view>
      </view>
    </view>
   <view v-show="shows" class="botm">已经到底啦 没有数据了亲</view>
  </view>
</template>

<script>
/**
 *  
 */
  export default {
    // 上拉加载函数方法
    onReachBottom(){
      console.log("上拉触发了");
      if (this.total == this.goodslist.length) {
        this.shows = true
        return
      }
      this.marpases.pagenum++
      this.getgoodslist()
    },
    onLoad(e){
      // console.log(e); // e=> {query:"大米"}
      this.marpases.query = e.query
      this.getgoodslist()
    },
    data(){
      return {
        marpases:{
          query:null ,// 查询的关键字
          pagenum:1 ,// 当前页码 默认显示第一页
          pagesize:20 // 每次刷新 显示20条数据 
         },
          goodslist:[] ,// 储存 关键字商品、 商品列表 数组
          total:0 ,// 商品总条数
          shows:false
      }
    },
    methods: {
      // 获取关键字 搜索到的 信息
      async getgoodslist(){
        let data = this.marpases
        let ser = await this.http({
          url:'/api/public/v1/goods/search',
          data
        })
        console.log(ser);
        
        this.goodslist.push(...ser.message.goods) // 关键字商品、 
        this.total = ser.message.total // 总条数 61
      },
      // 跳转到 详情页面
      goDetail (id) {
        uni.navigateTo({
          url: '/pages/goods/index?id=' + id
        })
      }
    }
  }
</script>

<style scoped lang="scss">
  .botm {
    height: 30rpx;
    margin-left: 9%;
    font-size: 57rpx;
  }
  .list{
    padding-top: 100rpx;
  }
  .filter {
    display: flex;
    height: 96rpx;
    line-height: 96rpx;
    border-bottom: 1rpx solid #ddd;

    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 9999;
    background-color: #fff;

    text {
      flex: 1;
      text-align: center;
      font-size: 30rpx;
      color: #333;

      &.active {
        color: #ea4451;
      }
    }
  }
  

  .item {
    display: flex;
    padding: 30rpx 20rpx 30rpx 0;
    margin-left: 20rpx;
    border-bottom: 1rpx solid #eee;

    &:last-child {
      border-bottom: none;
    }

    .pic {
      width: 200rpx;
      height: 200rpx;
      margin-right: 30rpx;
    }

    .meta {
      flex: 1;
      font-size: 27rpx;
      color: #333;
      position: relative;
    }

    .name {
      width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
    }

    .price {
      position: absolute;
      bottom: 0;

      color: #ea4451;
      font-size: 33rpx;

      text {
        font-size: 22rpx;
      }
    }
  }
</style>
