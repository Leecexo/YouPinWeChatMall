<template>
  <div>
    <!-- 搜索框 -->
    <div class="serch-input">
      <navigator url="/pages/search/main">
        <icon type="search" color="#999" size="12" /> {{keyword}}
      </navigator>
    </div>
    <!-- 选项卡 -->
    <div class="tabs">
      <div @click='tabHandle(index)' :class='{active: currentIndex === index}' :key='index' v-for='(item, index) in tabNames'
        class="tab-item">
        {{item}}
      </div>
    </div>
    <!-- 搜索结果 -->
    <div class="goods-list">
      <navigator class='goods-item' :key='index' v-for='(item, index) in list'>
        <img :src="item.goods_small_logo" mode="aspectFill" />
        <div class="goods-right">
          <h4>
            {{item.goods_name}}
          </h4>
          <div class="price">
            <span>￥</span>{{item.goods_price}}
          </div>
        </div>
      </navigator>
    </div>
  </div>
</template>
<script>
  import SearchBar from '../../utils/search.vue'
  import request from '../../utils/request';
  export default {
    data() {
      return {
        keyword: '', // input keywords内关键词
        tabNames: ['综合', '销量', '价格'], // 选项卡选项
        currentIndex: 0, // 当前选项卡选项
        list: [],
        pagenum: 1,
        total: 0
      }
    },
    methods: {
      // 点击时修改对应currentIndex的下标值
      tabHandle(index) {
        this.currentIndex = index
      }
    },
    // 获取当前关键词对应的搜索结果列表
    async onLoad(options) {
      this.keyword = options.query
      const res = await request("goods/search", "get", {
        query: this.keyword
      })
      console.log(res)
      const { message } = res.data
      this.list = message.goods
      this.pagenum = message.pagenum
      this.total = message.total
    },
  }
</script>
<style scoped>
  .search-bar {
    padding: 20rpx;
    background-color: #ffd161;
  }

  .serch-input {
    background-color: #fff;
    font-size: 28rpx;
    border-radius: 10rpx;
    height: 50rpx;
    line-height: 50rpx;
    border: 2rpx solid #eee;
    margin-top: 10rpx;
    padding-left: 20rpx;
    padding-top: 10rpx;
    /* box-sizing: border-box; */
  }

  .serch-input cion {
    display: inline;
    padding: 10rpx 0 0 10rpx;
    /* vertical-align: middle; */
  }

  .tabs {
    display: flex;
    width: 100%;
    justify-content: space-around;
    border-bottom: 1px #eee solid;
  }

  .tab-item {
    padding: 20rpx 0;
    flex: 1;
    text-align: center;
    font-size: 28rpx;
  }

  .active {
    color: red;
  }

  .goods-list {
    padding: 0 20rpx;
  }

  .goods-item {
    display: flex;
    justify-content: space-between;
    padding: 20rpx 0;
    border-bottom: 1px #eee solid;
  }

  .goods-item img {
    width: 200rpx;
    height: 200rpx;
    display: block;
    margin-right: 20rpx;
    flex-shrink: 0;
  }

  .goods-right {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .goods-right h4 {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
  }

  .price {
    color: red;
    font-size: 18px;
  }

  .price span {
    font-size: 14px;
  }
</style>
