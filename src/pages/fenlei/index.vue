<template>
<div>
    <xcx></xcx>
   
    <div class="left">
        <scroll-view scroll-y scroll-with-animation >
            <div class="catagory" :class="{active:index == sleccter}"  @click="check(index)" v-for="(item, index) in categoryList" :key="item.cat_id">
                {{item.cat_name}}
            </div>
        </scroll-view>
    </div>
    <div class="right">
       <scroll-view scroll-y scroll-with-animation >
            <img class="gangGao" src="/static/images/icon/titleImage.png" alt="">
            <div class="title" v-for="(item, index) in categoryList[sleccter].children" :key="item.cat_id">
                <h4>
                    /<span>{{item.cat_name}} </span>/
                </h4>
                <ul class="product">
                    <li v-for="(it, i) in item.children" :key="it.cat_id">
                        <a href="#">
                            <img class="image" :src="'https://autumnfish.cn/wx/'+it.cat_icon" alt="">
                            <text>{{it.cat_name}}</text>
                        </a>
                    </li>
                </ul> 
            </div> 
       </scroll-view>
    </div>
 
    
</div>
</template>

<script>
import xcx from "@/components/search";
import tool from "../../utils/index";

export default {
  components: {
    xcx
  },
  data: function() {
    return {
      categoryList: [],
      sleccter: 0
    };
  },
  onLoad() {
    tool
      .thenAjax({
        url: "api/public/v1/categories"
      })
      .then(res => {
        //   console.log(res)
        this.categoryList = res.data.message;
      });
  },
  methods: {
    check(index) {
      // console.log(index)
      this.sleccter = index;
    }
  }
};
</script>

<style lang="less">
a {
    color: #666;
    text-decoration: none;
    /*一般移动端清除a的点击高亮  透明就表示去掉*/
    -webkit-tap-highlight-color: transparent;
}
page {
  height: 100%;
}
page > view {
  height: 100%;
  display: flex;
}
.left {
  height: 100%;
  width: 200rpx;
  scroll-view {
    height: 100%;
    .catagory {
      height: 100rpx;
      background-color: #f4f4f4;
      text-align: center;
      line-height: 100rpx;
      font-size: 28rpx;
      color: #3a3a3a;
      border-bottom: 1px solid red;
      &.active {
        color: red;
        background-color: #ffffff;
        position: relative;
        &::before {
          content: "";
          position: absolute;
          width: 10rpx;
          height: 60rpx;
          background-color: red;
          left: 0;
          top: 10px;
        }
      }
    }
  }
}
.right {
  flex: 1;
  height: 100%;
  padding: 20rpx 15rpx;
  scroll-view {
    height: 100%;
    .gangGao {
      width: 100%;
      height: 180rpx;
      margin: 0 auto;
      display: block;
    }
    .title {
      text-align: center;
      >h4{
        font-size: 26rpx;
        padding: 30rpx 0;
        color: #ccc;
        >span {
          color: #000;
        }
      }
      .product {
        display: flex;
        flex-wrap: wrap;
        > li {
          width: 33.333%;
          padding: 10rpx 0;
          img {
            width: 95rpx;
            height: 90rpx;
            margin: 0 auto;
            display: block;
           
          }
           text {
            margin-top: 20rpx;
            font-size: 24rpx;
          }
          
        }
      }
    }
  }
}
</style>