<template>
  <view class="wrapper">
    <!-- 收货信息 -->
    <view class="shipment" v-if="address">
      <view class="dt">收货人: </view>
      <view class="dd meta">
        <text class="name">{{ address.userName }}</text>
        <text class="phone">{{ address.telNumber }}</text>
      </view>
      <view class="dt">收货地址:</view>
      <view class="dd">{{detailAddress}}</view>
    </view>
    <!-- 添加收货地址 按钮 -->
    <button v-else type="primary" @click="getAddress">添加收货地址</button>
    <!-- 购物车 -->
    <view class="carts">
      <view class="item">
        <!-- 店铺名称 --> 
        <view class="shopname">优购生活馆</view>
        <!-- 循环购物车 生成商品列表 -->
        <view class="goods" v-for="(item,index) in carts" :key="item.goods_id">
          <!-- 商品图片 -->
          <image class="pic" :src="item.goods_small_logo"></image>
          <!-- 商品信息 -->
          <view class="meta">
            <view class="name">{{item.goods_name}}</view>
            <view class="price">
              <text>￥</text>{{ item.goods_price }}<text>.00</text>
            <view @click="remov(index)"> 删除 </view>
            </view>
            <!-- 加减 -->
            <view class="amount">
              <text class="reduce" @click="reduceNum(index)">-</text>
              <!-- 监听 值改变事件 修改需要购买的数量 -->
              <input type="number" :value="item.goods_numbar" class="number" @input="setvalue(index,$event)">
              <text class="plus" @click="addnumber(index)">+</text>
            </view>
          </view>
          <!-- 选框 -->
          <view class="checkbox">
            <icon @click="togglo(index)" type="success" size="20" :color="item.goods_cheacked ? '#ea4451' :'#ccc'"></icon>
          </view>
        </view>
      </view>
    </view>
    <!-- 其它 -->
    <view class="extra">
      <label class="checkall">
        <icon @click="setAllChecked" type="success" :color="allchecked? '#ea4451' : '#ccc' " size="20"></icon>
        <!-- <icon type="success" color="#ccc" size="20"></icon> -->
        全选
      </label>
      <view class="total">
        合计: <text>￥</text><label>{{ totals }}</label><text>.00</text>
      </view>
      <view class="pay" @click="createOrder">结算({{ checkdegoods.length }})</view>
    </view>
  </view> 
</template>

<script>
  export default {
     computed:{
         // 拼接 收货地址
       detailAddress(){
        if (this.address) {
           return this.address.provinceName+this.address.cityName+this.address.countyName+this.address.detailInfo
        }
       },  
       // 计算购物车 商品总价
        totals(){
          let sums = 0
          // 遍历 每一项为 true (被选中) 的商品 进行计算
          this.checkdegoods.forEach(item => {
            // 购物车商品总价 => 商品价格 * 总数 相加 = 总价
             sums += item.goods_numbar * item.goods_price
          })
          return sums
        },
      // 计算属性 一般用作于 计算总价 计算总数 计算是否全选 等等
      // 计算属性 只要值变化 就会实时计算属性值的变化 后的数据
      checkdegoods(){ // 计算所有选中的 商品
          // 过滤 数组中的每一项 复选框 组成一个新数组
          let newgoods = this.carts.filter((item) =>{
            return item.goods_cheacked === true
          })
          // 过滤后的 结果返回
          return newgoods 
      },
      allchecked(){ 
        return this.carts.length === this.checkdegoods.length
      }
    }, 
    onShow(){
      this.carts = uni.getStorageSync('carts') || []
    },
      onLoad(){},
      data(){
        return {
          carts: [], // 购物车数据
          address:null // 收货地址
        }
      },
      methods:{
        // 点击结算按钮 跳转到 订单页面--- 支付列表
        createOrder(){
          // 需要3个条件 去判断
          // 1、 没有地址 不跳
          if (!this.address) {
            uni.showToast({
              title: '请填写收货地址!'
            })
            return
          }
          // 2、 没有选中的 商品不跳
          if (!this.checkdegoods.length >= 1) {
            uni.showToast({
              title: '请选择至少一件商品!'
            })
            return
          }
          // 3、 没有登陆 不跳 
          // 登陆后 会有一个 token 使用 token 进行判断 没有就是没登陆
          let token = uni.getStorageSync('token')
          if (!token) {
            uni.navigateTo({
              url: '/pages/auth/index'
            })
            return
          }
            console.log("全通过了");
        },
        // 点击删除按钮 删除商品
        remov(index){
          this.carts.splice(index,1)
          uni.setStorageSync('carts',this.carts)
        },
        // 点击添加 收货地址 按钮添加 收货地址
        getAddress(){
          uni.chooseAddress({
            // 收货地址 参数 result
             success:(result)=>{
              // 收货地址 赋值给数据中的 address
              this.address = result
            }
          }) 
        },
        // 点击全选按钮 实现全选 & 全不选 功能 
        setAllChecked(){
          if (this.allchecked) { 
             this.carts.forEach(item=>{
            item.goods_cheacked = false
          })
          } else {
            this.carts.forEach(item=>{
            item.goods_cheacked = true
          })
        }
          uni.setStorageSync('carts',this.carts)
        },
        // √ 复选框 选中 和 未选中
        togglo(index){
          // √ 选中了 就取反 未选中 未选中就取反
          this.carts[index].goods_cheacked = !this.carts[index].goods_cheacked
          uni.setStorageSync('carts',this.carts)
        },
        // 点击 + 号商品数量加1
        addnumber(index){
          // 当前要购买的商品 不能一直购买 需要限制一个购买数量
          let num = this.carts[index].goods_numbar
          if (num >= 10) return
          // 通过索引 找到需要更新的商品 对商品的数量进行 + 1
          this.carts[index].goods_numbar++
          // 购物车数据发生了改变  本地数据也需要写入更新 
          uni.setStorageSync('carts',this.carts)
        },
        // 点击 - 号商品数量加1
        reduceNum(index){
          console.log("我被点击了");
          
           // 当前要减去的商品 不能一直减 需要限制一个减去数量
          let num = this.carts[index].goods_numbar
          if (num <= 1) return
          // 通过索引 找到需要更新的商品 对商品的数量进行 - 1
          this.carts[index].goods_numbar--
          // 购物车数据发生了改变  本地数据也需要写入更新 
          uni.setStorageSync('carts',this.carts)
        },
        // 文本框 值改变事件 文本框输入值之后回车 触发该事件
        setvalue(index,e){
          // 获取到的 数字 是个字符串类型 这个类型 不正确 ×1 就可以进行隐式转换 成数字类型
          let a = e.detail.value * 1
          // 存入 本地之前 需要进行判断 一下 最多商品和最少商品
          if (a<=1) {
             a = 1
          } 
           if(a>=10) {
            a = 10
          }
            this.carts[index].goods_numbar = a
            uni.setStorageSync('carts',this.carts)
        },
      }
  }
</script>

<style scoped lang="scss">
  .shipment {
    height: 100rpx;
    line-height: 2;
    padding: 30rpx 30rpx 40rpx 30rpx;
    font-size: 27rpx;
    color: #333;
    background-color: #fff;
    background-image: url(http://static.botue.com/ugo/images/cart_border%402x.png);
    background-size: contain;
    background-repeat: no-repeat;
    background-position: bottom;

    .dt {
      width: 140rpx;
      float: left;
      clear: both;
    }

    .dd {
      padding-left: 160rpx;
    }

    .meta {
      padding-right: 50rpx;
    }

    text.phone {
      float: right;
    }
  }

  .carts {
    background-color: #f4f4f4;
    padding-bottom: 110rpx;
    overflow: hidden;

    .shopname {
      padding: 30rpx;
      margin-top: 20rpx;
      font-size: 30rpx;
      color: #333;
      background-color: #fff;
      border-top: 1rpx solid #eee;
      border-bottom: 1rpx solid #eee;
    }

    .goods {
      display: flex;
      padding: 30rpx 20rpx 30rpx 0;
      margin-left: 100rpx;
      border-bottom: 1rpx solid #eee;
      background-color: #fff;
  
      position: relative;

      .checkbox {
        width: 101rpx;
        height: 100%;
        background-color: #fff;

        display: flex;
        justify-content: center;
        align-items: center;

        position: absolute;
        left: -100rpx;
        top: 0;
      }

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

      .amount {
        position: absolute;
        bottom: 0;
        right: 20rpx;

        height: 48rpx;
        text-align: center;
        border: 1rpx solid #ddd;
        border-radius: 8rpx;

        display: flex;
        align-items: center;

        text {
          display: block;
          width: 60rpx;
          line-height: 48rpx;
          font-size: 36rpx;
          color: #ddd;
          text-align: center;
        }

        input {
          width: 60rpx;
          height: 48rpx;
          min-height: 48rpx;
          font-size: 27rpx;
          border-left: 1rpx solid #ddd;
          border-right: 1rpx solid #ddd;
        }
      }
    }
  }

  .extra {
    position: fixed;
    bottom: 0;
    /* #ifdef H5 */
    bottom: 50px;
    /* #endif */
    left: 0;
    z-index: 9;

    width: 750rpx;
    height: 96rpx;
    text-align: center;
    line-height: 96rpx;
    font-size: 36rpx;
    border-top: 1rpx solid #eee;
    background-color: #fff;
    color: #333;
    display: flex;

    .checkall {
      width: 140rpx;
      line-height: 1;
      margin-left: 25rpx;
      display: flex;
      align-items: center;

      icon {
        margin-right: 20rpx;
      }
    }

    .total {
      display: flex;
      justify-content: center;
      flex: 1;

      label, text {
        color: #ea4451;
        vertical-align: bottom;
        position: relative;
        bottom: -2rpx;
      }

      text {
        position: relative;
        bottom: -3rpx;
        font-size: 24rpx;

        &:first-child {
          margin-left: 10rpx;
        }
      }
    }

    .pay {
      width: 200rpx;
      background-color: #ea4451;
      color: #fff;
    }
  }
</style>

