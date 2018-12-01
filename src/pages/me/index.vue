<template>
  <div>
    <div class="user">
      <div class="box">
        <button open-type="getUserInfo" @getuserinfo="user">
          <img class="image" :src="img" alt="">
        </button>
        <span class="iconfont icon-shezhi"></span>
        <span class="iconfont icon-xiaoxi"></span>
        <p class="footer">
          {{text}}
        </p>
      </div>
    </div>
    <div class="content">
      <div class="box">
        <div class="big">
          <div class="product">
            <p class="num">0</p>
            <p>收藏的店铺</p>
          </div>
          <div class="product">
            <p class="num">0</p>
            <p>收藏的商品</p>
          </div>
          <div class="product">
            <p class="num">0</p>
            <p>关注的商品</p>
          </div>
          <div class="product">
            <p class="num">0</p>
            <p>我的足迹</p>
          </div>
        </div>
      </div>
      <div class="order">
        <div class="title">
          我的订单
        </div>
        <div class="list-box">
          <div class="list">
            <span class="iconfont icon-daifukuan"></span>
            <p class="opt-name">待付款</p>
          </div>
          <div class="list">
            <span class="iconfont icon-daishouhuo"></span>
            <p class="opt-name">待收获</p>
          </div>
          <div class="list">
            <span class="iconfont icon-tuikuan"></span>
            <p class="opt-name">退款/退货</p>
          </div>
          <div class="list">
            <span class="iconfont  icon-dingdan"></span>
            <p class="opt-name">全部订单</p>
          </div>
        </div>
      </div>
      <div class="address">
        收货地址管理
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      img: "/static/images/user.png",
      text: "登录/注册"
    };
  },
  methods: {
    user(res) {
      //   console.log(res);
      this.img = res.mp.detail.userInfo.avatarUrl;
      this.text = res.mp.detail.userInfo.nickName;
    }
  }
};
</script>

<style lang="less">
page {
  padding: 0;
}
.user {
  height: 405rpx;
  background-color: #eb4450;
  display: flex;
  align-items: center;
  justify-content: center;
  .box {
    position: relative;
    > button {
      height: 125rpx;
      width: 125rpx;
      padding: 0;
      border: 4rpx solid #fff;
      border-radius: 50%;

      .image {
        width: 100%;
        height: 100%;
        display: block;
        border: 50%;
      }
    }
    .iconfont {
      position: absolute;
      color: #fff;
      top: 55rpx;
      &.icon-shezhi {
        left: -95rpx;
      }
      &.icon-xiaoxi {
        left: 190rpx;
      }
    }
    .footer {
      display: block;
      text-align: center;
      margin-top: 20rpx;
      color: #fff;
      font-size: 28rpx;
    }
  }
}
.content {
  background-color: #f4f4f4;
  padding: 0 16rpx;

  .box {
    //   padding: 0 16rpx;
    background-color: #f4f4f4;
    .big {
      height: 120rpx;
      background-color: #ffffff;
      transform: translateY(-27rpx);
      display: flex;
      align-items: center;
      .product {
        flex: 1;
        color: #666666;
        > p {
          text-align: center;
          font-size: 26rpx;
          &.num {
            margin-bottom: 12rpx;
          }
        }
      }
    }
  }
  .order {
    height: 88rpx;
    background-color: #fff;
    //   padding: 0 16rpx;
    background-color: #f4f4f4;
    .title {
      line-height: 88rpx;
      padding-left: 31rpx;
      border-bottom: 2rpx solid #eaeaea;
      background-color: #ffffff;
    }
    .list-box {
      height: 166rpx;
      display: flex;
      align-items: center;
      .list {
        flex: 1;
        text-align: center;
        .iconfont {
          color: red;
          font-size: 58rpx;
          font-weight: 700;
        }
        p {
          font-size: 30rpx;
        }
      }
    }
  }
  .address {
    // display: flex;
    // align-items: center;
    // justify-content: space-between;
    
    .text {
      // padding-left: 32rpx;
    }
  }
}
</style>
