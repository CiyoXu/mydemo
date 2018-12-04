<template>
  <div>
    <div>
      <swiper class="swiper" indicator-dots autoplay circular>
        <block v-for="(item, index) in goodsInfo.pics" :key="item.goods_id">
          <swiper-item @click="preview(index)">
            <image :src="item.pics_mid"></image>
          </swiper-item>
        </block>
      </swiper>
    </div>
    <div class="price">
      <span class="fuhao">¥&nbsp;</span>
      <span>{{goodsInfo.goods_price}}</span>
    </div>
    <div class="title">
      <div class="left">
        <span class="name">{{goodsInfo.goods_name}}</span>
        <span class="expressage">快递:&nbsp;&nbsp;&nbsp;免运费</span>
      </div>
      <div class="right">
        <span class="iconfont icon-shoucang"></span>
        <span class="text">收藏</span>
      </div>
    </div>
    <div class="promotion">
      <span>促销</span>
      <span id="num">满1000减999</span>
    </div>
    <div class="promotion">
      <span>已选</span>
      <span class="slecet">黑色/S/1件</span>
    </div>
    <div class="promotion">
      <span>送至</span>
      <span id="address"  @click="location">{{address}}</span>
      <span class="iconfont icon-jiantouyou" @click="location"></span>
    </div>
    <div class="tab">
      <div class="opt" @click="change(0)" :class="{active:selecte == 0}">图文介绍</div>
      <div class="opt" @click="change(1)" :class="{active:selecte == 1}">规格参数</div>
    </div>
    <div class="content" v-show="selecte == 0">
      <div class="item">
        <wxParse :content="goodsInfo.goods_introduce" @preview="preview" @navigate="navigate" />
      </div>
    </div>
    <div class="content" v-show="selecte == 1">
      <div v-for="(item, index) in goodsInfo.attrs" :key="item.goods_id" class="row">
        <div class="col">{{item.attr_name}}</div>
        <div class="col">{{item.attr_value}}</div>
      </div>
    </div>
    <div class="control-box">
      <div>
        <span class='iconfont icon-kefu'></span>
        联系客服
      </div>
      <div @click="toCart">
        <span class='iconfont icon-gouwuche'></span>
        购物车
      </div>
      <input type="button" @click="addCat" value="加入购物车">
      <input type="button" value="立即购买">
    </div>
  </div>
</template>

<script>
import tool from "../../utils/index.js";
import wxParse from "qs-mpvue-wxparse";
export default {
  components: {
    wxParse
  },
  data: function() {
    return {
      goodsId: 0,
      //商品详情
      goodsInfo: [],
      selecte: 0,
      address: " 江西省/九江市/瑞昌市"
    };
  },
  onLoad(opt) {
    // console.log(opt);
    this.goodsId = opt.goods_id;
    //根据id调接口
    tool
      .thenAjax({
        url: `api/public/v1/goods/detail?goods_id=${this.goodsId}`
      })
      .then(res => {
        // console.log(res)
        this.goodsInfo = res.data.message;
      });
      let addressdata = wx.getStorageSync("detailAddress");
      if(addressdata){
        this.address = addressdata.address
      }
      
  },
  methods: {
    preview(index) {
      let pic = [];
      this.goodsInfo.pics.forEach(v => {
        pic.push(v.pics_big_url);
      });
      wx.previewImage({
        current: pic[index], // 当前显示图片的http链接
        urls: pic // 需要预览的图片http链接列表
      });
    },
    change(index) {
      // console.log(index)
      this.selecte = index;
    },
    location() {
      wx.chooseAddress({

        success: res => {
          // console.log("uuuuuuu")
          // this.address =
          //   res.provinceName + " " + res.cityName + " " + res.countyName;

           this.address =
            res.provinceName + " " + res.cityName + " " + res.countyName;

          wx.setStorage({
            key: "detailAddress",
            data: {
              address: this.address
            }
          });
        }
      });
    },
    //添加购物车
    addCat() {
      let cartData = wx.getStorageSync( 'cart' );
      if (cartData) {
        // console.log("有值")
        if (cartData[this.goodsId]) {
          cartData[this.goodsId] += 1;
        } else {
          cartData[this.goodsId] = 1;
        }
      } else {
        // 缓存里没值
        cartData = {};
        cartData[this.goodsId] = 1;
      }
      wx.setStorageSync(
        
        "cart", cartData
      );
    },
    //去购物车页面
    
    toCart(){
      wx.switchTab({ url: '/pages/car/main' });
      
    }
  }
};
</script>

<style scoped lang="less">
@import url("~qs-mpvue-wxparse/src/wxParse.css");
page {
  background-color: red!;
}
.swiper {
  height: 720rpx;
  image {
    height: 100%;
    width: 100%;
  }
}
.price {
  height: 100rpx;
  padding-left: 18rpx;
  background-color: #fff;
  > span {
    font-size: 45rpx;
    color: red;
    // padding-left: 18rpx;
    line-height: 100rpx;
  }
}
.title {
  padding-left: 18rpx;
  height: 200rpx;
  display: flex;
  justify-content: space-between;

  .left {
    width: 520rpx;

    .name {
      display: block;

      font-size: 34rpx;
      color: #343434;
      line-height: 50rpx;
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-box-orient: vertical;
      -webkit-line-clamp: 2;
    }
    .expressage {
      display: inline-block;
      font-size: 32rpx;
      color: #9a9a9a;
      padding-top: 41rpx;
    }
  }
  .right {
    padding-top: 16rpx;
    display: flex;
    flex-direction: column;
    flex: 1;
    font-size: 30rpx;
    color: #9e9e9e;
    text-align: center;
    position: relative;
    &::before {
      content: "";
      position: absolute;
      width: 2rpx;
      height: 60rpx;
      background-color: #ccc;
      left: 15rpx;
      top: 25rpx;
    }
  }
}
.promotion {
  margin-top: 10rpx;
  height: 100rpx;
  line-height: 100rpx;
  padding-left: 16rpx;
  font-size: 32rpx;
  #num {
    color: #ff2d4a;
    padding-left: 20rpx;
  }
  #address {
    padding-left: 10rpx;
  }
  > span:not(:first-child) {
    color: #9f9f9f;
    padding-left: 20rpx;
  }
}
.tab {
  display: flex;
  padding: 0 16rpx;
  height: 100rpx;
  .opt {
    flex: 1;
    text-align: center;
    line-height: 100rpx;
    &.active {
      color: #ff2d4a;
      border-bottom: 4rpx solid red;
    }
  }
}
.content {
  .item {
    padding: 6rpx 7rpx;

    view {
      margin: 0;
    }
    image {
      display: block;
    }
  }
  // 行跟列
  .row {
    display: flex;
    .col {
      flex: 1;
      height: 84rpx;
      border: 1rpx solid #cdcdcd;
      font-size: 22rpx;
      text-align: center;
      line-height: 84rpx;
      &:last-child {
        margin-left: -1rpx;
      }
    }
    // not选择器(取反)
    // .row 不是 第一个的话 往上挪1个rpx
    &:not(:first-child) {
      margin-top: -1rpx;
    }
  }
}
.control-box {
  display: flex;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  background-color: white;
  .iconfont {
    display: block;
    color: gray;
    font-size: 30rpx;
    margin-bottom: 10rpx;
  }

  > view {
    flex: 1;
    text-align: center;
    font-size: 24rpx;
    color: gray;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  input {
    width: 210rpx;
    height: 100rpx;
    color: white;
    text-align: center;
    line-height: 100rpx;
    font-size: 28rpx;
    border-radius: 0;
    // 必须是 第一个子元素才会命中
    // &:first-child{
    // 第一个 这种按钮
    &:first-of-type {
      background-color: hsl(40, 89%, 60%);
    }
    &:last-child {
      background-color: #ff2d4a;
    }
  }
}
</style>
