<template>
  <div>
    <!-- 搜索栏 -->
    <search-bar></search-bar>
    <!-- 轮播图 -->
    <div class="swiper-banner">
      <swiper indicator-dots="true">
        <swiper-item :key="item.goods_id" v-for="item in bannerData">
          <image :src="item.image_src" class="swiper-img" />
        </swiper-item>
      </swiper>
    </div>
    <!-- 菜单 -->
    <div class="menu">
      <div :key='index' v-for='(item, index) in menu' class="menu-item">
        <img :src="item.image_src">
      </div>
    </div>
    <!-- 商品列表 -->
    <div class="floor" :key='index' v-for='(item, index) in floor'>
      <!-- 楼层的头部 -->
      <div class="floor-title">
        <img :src="item.floor_title.image_src" mode="aspectFill">
      </div>
      <div class="floor-content">
        <div class="left">
          <img :src="item.product_list[0].image_src" mode="aspectFill">
        </div>
        <div class="right">
          <img v-if='i > 0' :key='i' v-for='(img, i) in item.product_list' :src="img.image_src" mode="aspectFill">
        </div>
      </div>
    </div>
    <!-- 返回顶部 -->
    <div class="toTop" @click='toTopHandle' v-if='isShow'>
      ︿
      <p>顶部</p>
    </div>
  </div>
</template>
<script>
  import request from '../../utils/request'
  import SearchBar from '../../utils/search.vue'
  export default {
    data() {
      return {
        bannerData: [{
          goods_id: 1,
          image_src: "https://images.unsplash.com/photo-1551334787-21e6bd3ab135?w=640"
        }], // 轮播图初始数据
        menu: Array.from(new Array(4)).map(() => ({
          image_src: 'https://www.zhengzhicheng.cn/pyg/icon_index_nav_4@2x.png',
        })), // 菜单初始数据，4个图标的数组
        floor: [], // 楼层初始数据
        isShow: false
      }
    },
    components: {
      "search-bar": SearchBar
    },
    methods: {
      async indexData(path) {
        // 定义通用请求解构方法
        let res = await request(path)
        return res.data.message
      },
      async getinitData() {
        // 轮播图获取数据方法
        this.bannerData = await this.indexData("home/swiperdata")
        // 菜单获取数据方法
        this.menu = await this.indexData("home/catitems")
        // 商品楼层获取数据方法
        this.floor = await this.indexData("home/floordata")
      },
      // 点击返回顶部按钮，返回首页
      toTopHandle() {
        mpvue.pageScrollTo({
          scrollTop: 0
        })
      }
    },
    created() {
      // 加载首页数据
      this.getinitData()
    },
    // 下拉120px 显示返回顶部按钮
    onPageScroll(event) {
      this.isShow = event.scrollTop > 120
    }
  }
</script>
<style scoped>
  .menu {
    display: flex;
    justify-content: space-around;
    margin: 20rpx 0;
  }

  .menu .menu-item img {
    width: 128rpx;
    height: 140rpx;
  }

  .floor {
    margin-top: 20rpx;
  }

  .floor-title {
    width: 100%;
  }

  .floor-title img {
    width: 100%;
    height: 60rpx;
    display: block;
  }

  .floor-content {
    display: flex;
    justify-content: space-between;
    width: 100%;
    padding: 20rpx;
    box-sizing: border-box;
  }

  .floor-content .left img {
    width: 232rpx;
    height: 385rpx;
    border-radius: 4px;
  }

  .floor-content .right {
    flex: 1;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    margin-left: 14rpx;
  }

  .floor-content .right img {
    width: 232rpx;
    height: 188rpx;
    border-radius: 4px;
  }

  .toTop {
    width: 100rpx;
    height: 100rpx;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.8);
    position: fixed;
    right: 40rpx;
    bottom: 40rpx;
    text-align: center;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .toTop p {
    font-size: 14px;
  }
</style>
