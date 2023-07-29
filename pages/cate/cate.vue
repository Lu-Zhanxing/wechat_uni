<template>
  <view>
    <view class="scroll-view-container">
      <scroll-view class="left-scroll-view" scroll-y="true" :style="{height:'100vh'}">
        <view class="left-scroll-view-item" :class="index==isActive?'active':''" v-for="(item,index) in cateList"
          :key="index" @click="changCateHandler(item,index)">{{item.cat_name}}</view>
      </scroll-view>
      <scroll-view class="right-scroll-view" scroll-y="true" :style="{height:wh+'px'}" :scroll-top="toTop">
        <view class="cate-lv2" v-for="(item,index) in cate2List" :key="index">
          <view class="cate-lv2-title">
            / {{item.cat_name}} /
          </view>
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item2,index2) in item.children" :key="index2" @click="gotoGoodsList(item2)">
              <image :src="item2.cat_icon"></image>
              <text>{{item2.cat_name}}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        wh: 0,
        cateList: [],
        isActive: 0,
        cate2List: [],
        toTop:0
      };
    },
    mounted() {
      this.wh = uni.getSystemInfoSync().windowHeight;
      this.getCateList()
    },
    methods: {
      async getCateList() {
        let {
          data: res
        } = await uni.$http.get("/api/public/v1/categories")
        if (res.meta.status !== 200) return uni.$showMsg()
        this.cateList = res.message
        this.cate2List = this.cateList[0].children
      },
      changCateHandler(item, index) {
        this.isActive = index
        this.cate2List = item.children
        this.toTop = this.toTop ? 0 : 1
      },
      gotoGoodsList(item2){
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item2.cat_id
        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;

    .left-scroll-view {
      width: 120px;

      .left-scroll-view-item {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 24rpx;
      }

      .active {
        background-color: #fff;
        position: relative;

        &::before {
          content: ' ';
          display: block;
          width: 3px;
          height: 30px;
          background-color: #c00000;
          position: absolute;
          left: 0;
          top: 50%;
          transform: translateY(-50%);
        }
      }
    }

    .cate-lv2-title {
      font-size: 26rpx;
      font-weight: bold;
      text-align: center;
      padding: 15px 0;
    }
    .cate-lv3-list{
      display: flex;
      flex-wrap: wrap;
      .cate-lv3-item{
        width: 33.33%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        margin-bottom: 20rpx;
        image{
          width: 120rpx;
          height: 120rpx;
        }
        text{
          font-size: 24rpx;
        }
      }
    }
  }
</style>