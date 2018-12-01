<template>
   <div class="header">
         <icon type="search"/>
         <input type="text" @click="search" class="search" placeholder="搜索">
     </div>
</template>

<script>
export default {
  methods: {
    search() {
      wx.navigateTo({
        url: "/pages/sousuo/main"
      });
    }
  }
};
</script>

<style lang="less">
page {
  // padding-top: 100rpx;
}
.header {
  background-color: #ff2d4a;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 999;
  width: 100%;
  padding: 20rpx 16rpx;
  icon {
    position: absolute;
    top: 30rpx;
    left: 50%;
    transform: translateX(-200%);
    color: #bebebe;
    z-index: 999;
  }
  .search {
    width: 100%;
    height: 60rpx;
    background-color: #fff;
    border-radius: 10rpx;
    text-align: center;
    color: #bebebe;
  }
}
</style>
