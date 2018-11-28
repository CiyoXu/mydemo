<template>
    <div>
        <div class="box">
          <icon class="search" type="search"  size="20"></icon>      
          <input class="button" v-model="search" type="text" @confirm="sub" placeholder="请输入商品名称">
          <input class="text" type="button" value="清空" @click="cancel">
       </div>
       <div class="history"  v-show="goodList.length ==0 ">
         <div class="top">
           <span class="text">历史搜索</span>
           <span class="iconfont icon-shanchu" @click="clear"></span>
         </div>
         <ul class="footer" >
           <!-- -->
           <li @longpress="del(index)" @click="searchHistory(item)" v-for="(item, index) in searchList" :key="index">
             {{item}}
           </li>
         </ul>
       </div>
       <div class="product"  v-show="goodList.length != 0 " >
         <div class="nav">
           <div class="text" @click="change(0)" :class="{active:selectIndex==0}">综合</div>
           <div class="text" @click="change(1)" :class="{active:selectIndex==1}">销量</div>
           <div class="text" @click="change(2)" :class="{active:selectIndex==2}">价格
             <div class="tuBiao">
                <span :class="{active:price==true}"   class="iconfont icon-jiantoushang"></span>
                <span :class="{active:price==false}"  class="iconfont  icon-jiantouxia"></span>
             </div>            
           </div>          
         </div>
         <ul>
           <li class="goods" v-for="(item, index) in product" :key="index">
              <img :src="item.goods_small_logo" alt="">
           <div class="right">
             <span class="title">
               {{item.goods_name}}
             </span>
             <p>¥<span>{{item.goods_price}}</span>.00</p>
           </div>
           </li>
         </ul>
       </div>
    </div>
</template>
<script>
import tool from "../../utils/index";

export default {
  data: function() {
    return {
      // 搜索的文本
      search: "",
      // 历史记录
      searchList: [],
      //商品数据
      goodList: [],
      // 保存原来的商品数据
      defineGoodsList:[],
      //选择的条件
      selectIndex: 0,
      // 价格排序
      price: true
    };
  },
  // 固定排序选择
  // onPageScroll(e){
  //   // console.log(e)
  //   if(e.scrollTop>60){

  //   }
  // },
  onLoad() {
    wx.getStorage({
      key: "history",
      success: res => {
        // console.log(res)
        this.searchList = res.data;
      },
      fail: data => {
        // console.log(data)
      }
      // 不管成功还是失败都会执行
      // complete:res=>{
      //     console.log(res)
      // }
    });
  },
  methods: {
    // 抽取出来调数据的方法
    getData(){
      wx.showLoading({
        title:"加载中...",
        mask:true
      })
       tool
        .thenAjax({
          url: `api/public/v1/goods/search?query=${this.search}`
        })
        .then(res => {
          // console.log(res)
          this.goodList = res.data.message.goods;

          // 保存默认的商品数据
          this.defineGoodsList = res.data.message.goods.concat([])

          // 关闭lodding
          wx.hideLoading();
          
        });
    },
    sub() {
      // console.log(e)
      let index = this.searchList.indexOf(this.search);
      // console.log(index);
      // console.log(this.searchList); //里面有东西

      if (index != -1) {
        //输入任何内容都为零
        // 不是-1表示存在,删除该下标
        this.searchList.splice(index, 1);
      }

      this.searchList.push(this.search);

      if (this.searchList.length > 10) {
        this.searchList.shift();
      }
      // console.log(e)
      wx.setStorage({
        key: "history",
        data: this.searchList
      });

      //搜索后调接口
     this.getData()
    },
    // 清空输入框的文本
    cancel() {
      // console.log("取消")
      this.search = "";
      this.goodList=[]
      this.defineGoodsList=[]
    },
    // 清空
    clear() {
      wx.showModal({
        title: "提示", //提示的标题,
        content: "是否清空搜索记录", //提示的内容,
        showCancel: true, //是否显示取消按钮,
        cancelText: "取消", //取消按钮的文字，默认为取消，最多 4 个字符,
        cancelColor: "#000000", //取消按钮的文字颜色,
        confirmText: "确定", //确定按钮的文字，默认为取消，最多 4 个字符,
        confirmColor: "#3CC51F", //确定按钮的文字颜色,
        success: res => {
          // console.log(res);
          if (res.confirm) {
            this.searchList = [];
            wx.setStorage({
              key: "history",
              data: this.searchList
            });
          }
        }
      });
    },
    // 长安删除
    del(index) {
      // console.log(index)
      this.searchList.splice(index, 1);
      wx.setStorage({
        key: "history",
        data: this.searchList
      });
    },
    //点击切换
    change(index) {
      this.selectIndex = index;
      //价格切换    
      this.price = !this.price     
    },
    // 点击历史记录搜索
    searchHistory(item){
      // console.log(item)
      this.search=item
      this.getData()

    }
  },
  computed: {
    product() {
      if (this.selectIndex == 0) {
        return this.defineGoodsList;
      } else if (this.selectIndex == 1) {
        // console.log(this.goodList)
        this.goodList.sort((a, b) => {
          // console.log(a)
          // console.log(b)
          return a.cat_id - b.cat_id;
        });
        return this.goodList;
      }else if(this.selectIndex ==2){
        this.goodList.sort((a, b) => {
          // console.log(a)
          // console.log(b)
          if(this.price==true){
            return a.goods_price - b.goods_price;
          }else{
            return b.goods_price - a.goods_price;
          }         
        });
         return this.goodList;
      }
    }
  }
};
</script>
<style lang="less">
page {
  padding-top: 0;
}
.box {
  height: 120rpx;
  width: 100%;
  background-color: #eeeeee;
  padding: 30rpx 16rpx;
  display: flex;
  position: relative;
  border-radius: 5rpx;
  .search {
    position: absolute;
    left: 40rpx;
    // align-items: center;
    top: 45rpx;
    z-index: 999;
  }
  .button {
    flex: 1;
    height: 60rpx;
    // width: 538rpx;
    background-color: #ffffff;
    font-size: 26rpx;
    padding-left: 70rpx;
    // left: 70rpx;
    margin-right: 20rpx;
  }
  .text {
    width: 156rpx;
    height: 60rpx;
    text-align: center;
    line-height: 60rpx;
    background-color: #eeeeee;
  }
}
// 历史列表
.history {
  .top {
    height: 88rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 30rpx 0 16rpx;

    .text {
      color: #565656;
      font-size: 32rpx;
    }
    .iconfont {
      width: 30rpx;
      height: 30rpx;
      color: #cccccc;
    }
  }
  .footer {
    display: flex;
    flex-wrap: wrap;
    padding-left: 16rpx;
    li {
      height: 64rpx;
      // width: 98rpx;
      background-color: #dddddd;
      color: #4c4c4c;
      text-align: center;
      line-height: 64rpx;
      margin-top: 16rpx;
      margin-right: 30rpx;
      padding: 0 26rpx;
      font-size: 28rpx;
    }
  }
}
// 搜素的商品列表
.product {
  .nav {
    height: 100rpx;
    display: flex;
    align-items: center;
    border-bottom: 1rpx solid #ccc;
    .text {
      flex: 1;
      text-align: center;
      font-size: 30rpx;
      &.active {
        color: red;
      }
    }
    .text:last-child {
      display: flex;
      align-items: center;
      justify-content: center;
      .tuBiao {
        .iconfont {
          display: block;
          color: #000;
          font-weight: 700;
          font-size: 26rpx;
          &.active {
            color: yellow;
          }
        }
      }
    }
  }

  //内容
  .goods {
    height: 260rpx;
    background-color: #ffffff;
    border-bottom: 1rpx solid #ccc;
    display: flex;
    img {
      width: 232rpx;
      height: 100%;
      width: 230rpx;
    }
    .right {
      flex: 1;
      padding: 38rpx 25rpx;
      .title {
        font-size: 32rpx;
        overflow: hidden;
        margin-bottom: 70rpx;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp: 2;
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp: 2;
        line-height: 48rpx;
      }
      p {
        display: inline;
        color: red;
        font-size: 24rpx;

        span {
          color: red;
          font-size: 32rpx;
        }
      }
    }
  }
}
</style>


