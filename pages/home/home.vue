<template>
  <view>
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,index) in swiperList" :key="index">
        <view class="swiper-item">
          <image :src="item.image_src"></image>
        </view>
      </swiper-item>
    </swiper>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        swiperList:[]
      };
    },
    mounted() {
      this.getSwiperList()
    },
    methods:{
      async getSwiperList(){
        let {data:result} = await uni.$http.get("/api/public/v1/home/swiperdata")
        if(result.meta.status != 200){
          uni.showToast({
            icon: "none",
            title: '数据请求失败',
            duration: 1500
          })
        }else{
          this.swiperList = result.message
        }
      }
    }
  }
</script>

<style lang="scss">
  view{
    height: 330rpx;
    .swiper-item,image{
      width: 100%;
      height: 100%;
    }
  }
</style>
