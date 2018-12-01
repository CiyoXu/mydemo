<template>
 <div>
    <xcx></xcx>
     <!-- 轮播图 -->
     <swiper indicator-dots autoplay circular>     
             <swiper-item v-for="(item, index) in swiper" :key="item.name">
                 <image mode="aspectFill" :src="item.image_src"></image>
             </swiper-item>        
     </swiper>
     <!-- 分类 -->  
    <ul class="category">
        <li v-for="(item, index) in fenLei" :key="item.name">
            <img :src="item.image_src" alt="">
            <p>{{item.name}}</p>
        </li>
    </ul>
   
    <!-- 楼层数据 -->
    <div class="fooler" v-for="(item, index) in foolList" :key="index">
      <div class="title">
        <text class="text">{{item.floor_title.name}}</text>
        <img class="image" :src="item.floor_title.image_src" alt="">
      </div>
      <div class="content">
        <a class="nav" href="#" v-for="(it, i) in item.product_list" :key="i">
          <img class="image" :src="it.image_src" alt="">
        </a>
      </div>
    </div>

    <!-- 底部 -->
    
    <div class="footer">
      <span class="iconfont icon-xiao"></span>我是有底线的
    </div>
    <div class="top" v-show="isshow" @click="top">
      <span class="iconfont icon-jiantoushang"></span>顶部
    </div>


 </div>
</template>

<script>
import tool from "../../utils/index";
import xcx from "../../components/search"
// import xcx from '@/components/search'; 

export default {
  // 自己注册的组件
  components:{
    xcx
  },
  data: function() {
    return {
      swiper: [],
      fenLei: [],
      foolList: [],
      isshow:false
    };
  },
  onLoad() {
    tool
      .thenAjax({
        url: "api/public/v1/home/swiperdata"
      })
      .then(res => {
        // console.log(res)
        this.swiper = res.data.message;
        return tool.thenAjax({
          url: "api/public/v1/home/catitems"
        });
      })
      .then(res => {
        // console.log(res)
        this.fenLei = res.data.message;
        return tool.thenAjax({
          url: "api/public/v1/home/floordata"
        });
      })
      .then(res => {
        //   console.log(res)
        this.foolList = res.data.message;
      });
  },
  onPageScroll(e){
 
    if(e.scrollTop>100){
      this.isshow=true
    }else{
      this.isshow=false
    }
  },
  methods:{
    top(){
      wx.pageScrollTo({
        scrollTop:0,
        duration:300
      })
    }
  }
};
</script>


<style lang="less">

// 轮播图
swiper {
  padding-top: 100rpx;
  height: 340rpx;
  image {
    display: block;
    width: 100%;
    height: 100%;
  }
}
//分类
.category {
  width: 100%;
  height: 200rpx;
  background-color: pink;
  display: flex;

  li {
    flex: 1;
    padding-top: 30rpx;
    padding-bottom: 30rpx;
    img {
      width: 100rpx;
      height: 100rpx;
      display: block;
      margin: 0 auto 14rpx;
    }
    p {
      text-align: center;
      color: #333333;
    }
  }
}
// 楼层
.fooler {
  .title {
    position: relative;
    .text {
      position: absolute;
      color: #ff7b94;
      font-weight: 700;
      font-size: 50rpx;
      left: 20rpx;
      top: 10rpx;
    }
    .image {
      height: 85rpx;
      width: 100%;
    }
  }
  .content {
    padding: 20rpx 16rpx;
    height: 440rpx;
    .nav {
      display: block;
      float: left;
      width: 33.333%;
      height: 100%;
      padding: 5rpx;
      box-sizing: border-box;
    }
    .image {
      display: block;
      width: 100%;
      height: 100%;
    }
    // 只要不是第一个 nav
    .nav:not(:first-child) {
      height: 50%;
    }
  }
}
// 底部
.footer{
  height: 140rpx;
  background-color: #f4f4f4;
  color: #999;
  text-align: center;
  line-height: 140rpx;
  font-size: 28rpx;
 
}
// 顶部
 .top{
    width: 80rpx;
    height: 80rpx;
    background-color: #fafafa;
    color: #828282;
    position: fixed;
    bottom: 20rpx;
    right: 20rpx;
    border-radius: 50%;
    font-size: 24rpx;
    text-align: center;
    .iconfont{
      display: block
    }
  }
</style>