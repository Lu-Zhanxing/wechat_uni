<template>
  <view>
    <!-- 轮播图 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,index) in swiperList" :key="index">
        <navigator class="swiper-item" :url="`/subpkg/goods_detail/goods_detail?id=${item.goods_id}`">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>

    <!-- 分类导航 -->
    <view class="nav-list">
      <view class="nav-list-item" v-for="(item,index) in navList" :key="index" @click="navClickHandler(item)">
        <image :src="item.image_src"></image>
      </view>
    </view>

    <!-- 楼层信息 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item,index) in floorList" :key="index">
        <view>
          <image :src="item.floor_title.image_src" class="item-title"></image>
        </view>
        <view class="item-body">
          <navigator class="item-body-left" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}"
              mode="widthFix">
            </image>
          </navigator>
          <view class="item-body-right">
            <navigator v-for="(item2,index2) in item.product_list" :key="index2" v-if="index2 !== 0" :url="item2.url">
              <image :src="item2.image_src" :style="{width:item2.image_width + 'rpx'}" mode="widthFix"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>

  </view>
</template>

<script>
  export default {
    data() {
      return {
        swiperList: [],
        navList: [],
        floorList: []
      };
    },
    mounted() {
      this.getSwiperList();
      this.getNavList();
      this.getFloorList()
    },
    methods: {
      async getSwiperList() {
        const {
          data: result
        } = await uni.$http.get("/api/public/v1/home/swiperdata")
        if (result.meta.status != 200) {
          return uni.$showMsg()
        }
        this.swiperList = result.message
      },

      async getNavList() {
        const {
          data: result
        } = await uni.$http.get("/api/public/v1/home/catitems")
        if (result.meta.status != 200) {
          return uni.$showMsg()
        }
        this.navList = result.message
      },

      navClickHandler(item) {
        if (item.name == "分类") {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },

      async getFloorList() {
        const {
          data: result
        } = await uni.$http.get("/api/public/v1/home/floordata")
        if (result.meta.status != 200) {
          return uni.$showMsg()
        }
        
        // 通过双层 forEach 循环，处理 URL 地址
        result.message.forEach(floor => {
          floor.product_list.forEach(item => {
            item.url = '/subpkg/goods_list/goods_list?' + item.navigator_url.split("?")[1]
          })
        })
        
        this.floorList = result.message
      },
    }
  }
</script>

<style lang="scss">
  // 轮播图
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  // 分类导航
  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;

    image {
      width: 128rpx;
      height: 140rpx;
    }
  }

  // 楼层
  .floor-list {
    .item-title {
      width: 100%;
      height: 60rpx;
    }

    .item-body {
      display: flex;
      padding-left: 10rpx;

      .item-body-right {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
      }
    }
  }
</style>