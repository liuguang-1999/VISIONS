<template>
  <view class="wrapper">
    <!-- 商品图片 -->
    <swiper class="pics" indicator-dots indicator-color="rgba(255, 255, 255, 0.6)" indicator-active-color="#fff">
      <swiper-item v-for="item in goods.pics" :key="item">
        <image :src="item.pics_big_url"></image>
      </swiper-item>
    </swiper>
    <!-- 基本信息 -->
    <view class="meta">
      <view class="price">￥{{ goods.goods_price }}</view>
      <view class="name">{{ goods.goods_name }}</view>
      <view class="shipment">快递: 免运费</view>
      <text class="collect icon-star">收藏</text>
    </view>
    <!-- 商品详情 -->
    <view class="detail">
      <rich-text :nodes="goods.goods_introduce"></rich-text>
    </view>
    <!-- 操作 -->
    <view class="action">
      <button open-type="contact" class="icon-handset">联系客服</button>
      <text class="cart icon-cart" @click="goCart">购物车</text>
      <text class="add" @click="addcarte">加入购物车</text>
      <text class="buy" @click="createOrder">立即购买</text>
    </view>
  </view>
</template>

<script>
  export default {
    onLoad(e){
      // console.log(e);
        this.id = e.id
      // 页面初始化 执行获取 id 对应的数据
      this.getdatas()
    },
    data(){
      return {
        id:null ,// 商品列表传过来的 id
        goods:null ,// 储存 请求回来的数据 
        // 本地里面有数据  就取出来 从尾部添加 一条
        // 假如是个 空数组 直接添加数据
        carts: uni.getStorageSync('carts') || [] // 购物车 数组储存商品
      }
    },
    methods: {
        // 加入 购物车  功能  当前商品 存入到本地
        addcarte(){
         // 需要用到的 数据解构赋值 出来 不需需要用到全部数据 
         // 商品id 价格 购买数量 名字 图片
         let {goods_id,goods_price,goods_name,goods_small_logo}=this.goods
         // 判断 carts购物车 是否有当前商品 有就数量加一 没有就尾部追加
         // 判断商品 id  是否 和当前商品id 一样
         let index = this.carts.findIndex(item =>  item.goods_id===this.goods.goods_id)
         if (index === -1) {
            this.carts.push({
           goods_id,
           goods_price,
           goods_name,
           goods_small_logo,
           goods_numbar:1 ,// 默认 1条数据
           goods_cheacked:true // 购物车 商品选中或未被选中 处理参数
         })
         uni.showToast({
           title: '加入购物车成功'
         })
         }else{
           this.carts[index].goods_numbar++
         }
        // 存入本地操作 
         uni.setStorageSync('carts', this.carts);
        },
        // 通过 id 获取对应数据
        async getdatas(){
          let ser =await this.http({
            url:"/api/public/v1/goods/detail",
            data:{
              goods_id:this.id // 需要请求的商品 id
            }
          })
          console.log(ser);
          this.goods = ser.message
        },
      goCart () {
        uni.switchTab({
          url: '/pages/cart/index'
        })
      },
      createOrder () {
        uni.navigateTo({
          url: '/pages/order/index'
        })
      }
    }
  }
</script>

<style scoped lang="scss">
  .wrapper {
    margin-bottom: 100rpx;
    background-color: #f4f4f4;
  }

  .pics {
    height: 640rpx;
  }
  
  .meta {
    height: 250rpx;
    line-height: 1;
    padding: 30rpx 180rpx 30rpx 20rpx;
    box-sizing: border-box;
    background-color: #fff;
    position: relative;

    .price {
      font-size: 36rpx;
      color: #ea4451;
      margin-bottom: 20rpx;
    }

    .name {
      color: #333;
      line-height: 1.4;
      font-size: 33rpx;

      display: -webkit-box;
      -webkit-box-orient: vertical;
      -webkit-line-clamp: 2;
      overflow: hidden;
    }

    .shipment {
      font-size: 27rpx;
      color: #999;
      position: absolute;
      bottom: 30rpx;
    }

    .collect {
      width: 140rpx;
      height: 88rpx;
      text-align: center;
      box-sizing: border-box;
      border-left: 1rpx solid #ddd;
      font-size: 24rpx;
      color: #999;

      position: absolute;
      right: 10rpx;
      top: 91rpx;
    }

    [class*="icon-"]::before {
      display: block;
      font-size: 45rpx;
      margin-bottom: 10rpx;
    }
  }

  .detail image {
    width: 100%;
    height: 480rpx;
    margin-top: 20rpx;
  }

  .action {
    width: 100%;
    height: 98rpx;
    background-color: #fff;

    position: fixed;
    left: 0;
    bottom: 0;

    display: flex;
    align-items: center;

    text {
      display: block;
    }

    .add, .buy {
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      width: 210rpx;
      text-align: center;
      font-size: 27rpx;
      color: #fff;
    }

    .add {
      background-color: #f4b73f;
    }

    .buy {
      background-color: #ea4451;
    }

    button {
      padding: 0;
      border-radius: 0;
      background-color: #fff;

      &::after {
        border: none;
      }
    }

    button, .cart {
      flex: 1;
      text-align: center;
      color: #989898;
      font-size: 24rpx;
      box-sizing: border-box;
    }

    [class*="icon"]::before {
      display: block;
      font-size: 45rpx;
      margin-bottom: 2rpx;
    }
  }
</style>
