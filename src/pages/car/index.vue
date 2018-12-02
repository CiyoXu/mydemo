<template>
 <div>
     <div class="address" @click="chooseAddress">
        <div class="name" >收货人:&nbsp;&nbsp;&nbsp;&nbsp;姐姐家</div>
        <div class="right">
            <span class="num">1111111111</span>
            <span class="iconfont icon-jiantouyou"></span>
        </div>
     </div>
     <div class="sure">
         收货地址:&nbsp;&nbsp;你姐姐你你你及有 <span></span>
     </div>
 </div>
</template>

<script>
import tool from "../../utils/index"

export default {
    data:function(){
        return {
            cartList:[],
            username:"",
            num:0,
            address:""
        }
    },
    onLoad(){
        let cartData = wx.getStorageSync( 'cart' );
        let ids = ""
        for (const key in cartData) {
            //每个key就是商品的id
            ids += key+","
            // console.log(ids)
            // console.log(ids.slice(0,-1))
            
        }
        ids = ids.slice(0,-1)
        tool.thenAjax({
            url:`api/public/v1/goods/goodslist?goods_ids=${ids}`
        })
        .then(res=>{
            // console.log(res)
            this.cartList = res.data.message
        })
    },
    methods:{
        chooseAddress(){
            wx.chooseAddress({
              success: (res=>{
                   console.log(res.userName);
                console.log(res.postalCode);
                console.log(res.provinceName);
                console.log(res.cityName);
                console.log(res.countyName);
                console.log(res.detailInfo);
                console.log(res.nationalCode);
                console.log(res.telNumber);
              })
            });
            
        }
    }

}
</script>

<style lang="less">
    .address{
        height: 104rpx;
        padding: 46rpx 33rpx 65rpx 18rpx;
        display: flex;
        justify-content: space-between;
        color: #343434;
        .right{
            .num{
                margin-right: 50rpx;
            }
            .iconfont{
                color: #bababa;
            }
        }
    }
    .sure{
        padding-left: 18rpx;
        color: #343434;;
        height: 104rpx;
        line-height: 104rpx;
        border-bottom: 1px solid #f4f4f4;
    }
   
</style>
