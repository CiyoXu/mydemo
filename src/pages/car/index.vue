<template>
  <div>
    <div class="address" @tap="chooseAddress">
      <div class="name">收货人:&nbsp;&nbsp;&nbsp;&nbsp;{{name}}</div>
      <div class="right">
        <span class="num">{{telNumber}}</span>
        <span class="iconfont icon-jiantouyou"></span>
      </div>
    </div>
    <div class="sure">
      收货地址:&nbsp;&nbsp;{{address}}
      <span></span>
    </div>
    <!-- 列表 -->

    <div class="list-box">
      <div class="title">
        <span class="iconfont icon-dianpu"></span>
        优购生活馆
      </div>
      <!-- 商品列表 -->
      <ul class="items">
        <li class='item' v-for="(item, index) in cartList" :key="item.goods_id">
          <div class="left">

            <input color="#EB4450" type="radio" :checked="item.selected" @click="changed(index)" class="radio">
          </div>
          <div class="right">
            <img class="img" :src="item.goods_small_logo" alt="">
            <div class="right-content">
              <div class="right-title">{{item.goods_name}}</div>
              <div class="control">
                <span class="price">¥
                  <span class="price-num">{{item.goods_price}}</span>.00
                </span>
                <div class="num-control">
                  <span @click="sub(index)" class="sub btn">-</span>
                  <span class="buy-num">{{item.buyCont}}</span>
                  <span @click="add(index)" class="add btn">+</span>
                </div>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>

    <!-- 底部的结算区域 -->
    <div class="pay-box">
      <input type="radio" @click="checkAll" :checked="IscheckAll" color="#EB4450" class="check-all">
      <span class='info'>全选</span>
      <div class="price-box">
        <div class="top">
          合计:
          <span class="span">¥
            <span class="price">{{price}}</span>.00</span>
        </div>
        <div class="bottom">
          包含运费
        </div>
      </div>
      <button @click="payOrder" class="button">结算({{num}})</button>
    </div>
  </div>
</template>

<script>
import tool from "../../utils/index";
import { setTimeout } from "timers";

export default {
  data: function() {
    return {
      //购物车李购买的数据
      cartList: [],
      //地址
      name: "",
      telNumber: "",
      address: "",
      IscheckAll: true
    };
  },
  onShow() {
    //购物车的逻辑
    let cartData = wx.getStorageSync("cart");
    let ids = "";
    for (const key in cartData) {
      //每个key就是商品的id
      ids += key + ",";
      // console.log(ids)
      // console.log(ids.slice(0,-1))
    }
    ids = ids.slice(0, -1);
    tool
      .thenAjax({
        url: `api/public/v1/goods/goodslist?goods_ids=${ids}`
      })
      .then(res => {
        res.data.message.forEach(e => {
          //添加勾选的字段默认为true
          e.selected = true;
          //添加购买的数量
          //通过商品的id拿到缓存中的商品数量
          e.buyCont = cartData[e.goods_id];
        });
        this.cartList = res.data.message;
      });

    //获取用户地址缓存
    let addressDdata = wx.getStorageSync("address");
    // console.log(addressDdata);
    if (addressDdata) {
      this.name = addressDdata.name;
      this.telNumber = addressDdata.telNumber;
      this.address = addressDdata.address;
    }
  },

  methods: {
    chooseAddress() {
      wx.chooseAddress({
        success: res => {
          //   console.log(res);
          this.name = res.userName;
          this.telNumber = res.telNumber;
          this.address =
            res.provinceName + " " + res.cityName + " " + res.countyName;

          wx.setStorage({
            key: "address",
            data: {
              name: this.name,
              telNumber: this.telNumber,
              address: this.address
            }
          });
        }
      });
    },
    //单选
    changed(index) {
      // console.log(index)
      // 选择的状态
      this.cartList[index].selected = !this.cartList[index].selected;
  
      this.IscheckAll =
        this.cartList.length ==
        this.cartList.filter(v => {
          return v.selected;
        }).length;

      //    wx.setStorage({
      //   key: "satus",
      //   data: this.cartList[index].selected
      // });
    },
    //全选
    checkAll() {
      this.IscheckAll = !this.IscheckAll;
      this.cartList.forEach(v => {
        v.selected = this.IscheckAll;
      });
      //  wx.setStorage({
      //   key: "satusAll",
      //   data: this.IscheckAll
      //    });
    },
    sub(index) {
      if (this.cartList[index].buyCont != 1) {
        this.cartList[index].buyCont--;
      } else {
        return (this.cartList[index].buyCont = 1);
      }
      //保存到缓存中并且同步更新
      let cartData = wx.getStorageSync("cart");
      //  获取商品的id,根据id去缓存中查找对应的数量然后赋值
      let idData = this.cartList[index].goods_id;
      cartData[idData] = this.cartList[index].buyCont;
      //保存修改后的缓存的数据
      wx.setStorage({
        key: "cart",
        data: cartData
      });
    },
    add(index) {
      this.cartList[index].buyCont++;
      let cartData = wx.getStorageSync("cart");
      //  获取商品的id,根据id去缓存中查找对应的数量然后赋值
      let idData = this.cartList[index].goods_id;
      cartData[idData] = this.cartList[index].buyCont;
      //保存修改后的缓存的数据
      wx.setStorage({
        key: "cart",
        data: cartData
      });
    },
    // 结算
    payOrder() {
      let token = wx.getStorageSync("token");
      if (!token) {
        //提示登录
        wx.showLoading({
          title: "请登录", //提示的内容,
          mask: true, //显示透明蒙层，防止触摸穿透,
          success: res => {
            setTimeout(function() {
              wx.hideLoading();
            }, 2000);
          }
        });
      }
    }
  },
  computed: {
    price() {
      let totalPrice = 0;
      this.cartList.forEach(v => {
        if (v.selected == true) {
          totalPrice += v.goods_price * v.buyCont;
        }
      });
      return totalPrice;
    },
    num() {
      let totalNum = 0;
      this.cartList.forEach(v => {
        if (v.selected == true) {
          totalNum += v.buyCont;
        }
      });
      return totalNum;
    }
  }
};
</script>

<style lang="less">
.address {
  height: 104rpx;
  padding: 46rpx 33rpx 65rpx 18rpx;
  display: flex;
  justify-content: space-between;
  color: #343434;
  .right {
    .num {
      margin-right: 50rpx;
    }
    .iconfont {
      color: #bababa;
    }
  }
}
.sure {
  padding-left: 18rpx;
  color: #343434;
  height: 104rpx;
  line-height: 104rpx;
}
// 购物车数据列表
.list-box {
  padding-top: 5rpx;
  background-color: #f4f4f4;
  .title {
    height: 88rpx;
    border-bottom: 1rpx solid #f4f4f4;
    line-height: 88rpx;
    padding-left: 18rpx;
    background-color: white;
    font-size: 32rpx;
  }
}
// 购物车每一个商品的布局
.items {
  background-color: white;
  .item {
    height: 208rpx;
    display: flex;
    align-items: center;
    .left {
      width: 90rpx;
      text-align: center;
    }
    .right {
      flex: 1;
      padding-right: 20rpx;
      border-bottom: 1rpx solid gray;
      display: flex;
      align-items: center;
      overflow: hidden;
      height: 208rpx;
      .img {
        width: 160rpx;
        height: 160rpx;
      }
      .right-content {
        flex: 1;
        margin-left: 24rpx;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        height: 208rpx;
        .right-title {
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 2;
          font-size: 32rpx;
          padding-top: 20rpx;
        }
        .control {
          display: flex;
          justify-content: space-around;
          .price {
            color: #eb4450;
            font-size: 24rpx;
            .price-num {
              font-size: 32rpx;
            }
          }
          .num-control {
            padding-bottom: 15rpx;
            .btn {
              display: inline-block;
              width: 52rpx;
              height: 42rpx;
              text-align: center;
              line-height: 42rpx;
              border: 1rpx solid #000;
              box-sizing: border-box;
            }
            .buy-num {
              display: inline-block;
              font-size: 22rpx;
              text-align: center;
              width: 80rpx;
              height: 42rpx;
            }
          }
        }
      }
    }
  }
}

.pay-box {
  position: fixed;
  bottom: 0;
  left: 0;
  background-color: white;
  height: 98rpx;
  display: flex;
  align-items: center;
  padding-left: 25rpx;
  justify-content: space-between;
  width: 100%;
  .radio {
    // margin: 0 25rpx;
  }
  .info {
    font-size: 26rpx;
  }
  .price-box {
    display: flex;
    flex-direction: column;
    .top {
      font-size: 30rpx;
      .span {
        color: red;
        font-size: 22rpx;
        .price {
          font-size: 26rpx;
          color: red;
        }
      }
    }
    .bottom {
      padding-top: 10rpx;
      color: #ccc;
      font-size: 22rpx;
    }
  }
  .button {
    width: 230rpx;
    height: 100%;
    border-radius: 0;
    background-color: #eb4450;
    color: white;
    margin: 0;
    line-height: 98rpx;
  }
}
</style>
