<template>
  <div>
    <!-- 搜索 -->
    <search-bar></search-bar>
    <!-- 菜单和内容 -->
    <div class="content">
      <div class="left">
        <div @click='changeBrand(index)' :class='{active: currentIndex === index}' :key='item.cat_id' v-for='(item, index) in catelist'
          class="menu-item">
          {{item.cat_name}}
        </div>
      </div>
      <div class="right">
        <div :key='item1.cat_name' v-for='item1 in rightData' class="brand-item">
          <div class="brand-title">{{item1.cat_name}}</div>
          <div class="brand-list">
            <div @click="clickGoodsList(img.cat_name,item1.cat_name)" :key='i' v-for='(img, i) in item1.children' class="brand">
              <img :src="img.cat_icon" mode="aspectFill">
              <p>{{img.cat_name}}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import request from '../../utils/request';
  import SearchBar from '../../utils/search.vue';
  export default {
    data() {
      return {
        catelist: [], // 一级栏目数组
        currentIndex: 0, // 当前一级栏目index值
        rightData: [],// 二级三级栏目数组
      }
    },
    methods: {
      clickGoodsList(cat_name, car_name) {
        // console.log(cat_name, car_name)
        mpvue.navigateTo({
          url: "/pages/goods_list/main?cat_name=" + cat_name + "&car_name=" + car_name
        })
      },
      async getcatData() {
        let res = await request("categories")
        this.catelist = res.data.message
      },
      // 点击切换一级栏目，对应修改二级和三级栏目
      async changeBrand(index) {
        this.currentIndex = index
        await this.getcatData()
        this.rightData = this.catelist[this.currentIndex].children
      }
    },
    // 搜索栏组件
    components: {
      "search-bar": SearchBar
    },
    // 加载首屏数据
    async created() {
      await this.getcatData()
      this.rightData = this.catelist[this.currentIndex].children
    },
  }
</script>
<style>
  .content {
    display: flex;
    position: fixed;
    left: 0;
    right: 0;
    top: 88rpx;
    bottom: 0;
  }

  .menu-item {
    height: 102rpx;
    line-height: 102rpx;
    text-align: center;
    position: relative;
  }

  .active {
    background: #fff;
    color: red;
  }

  .active ::before {
    content: "";
    display: block;
    width: 4px;
    height: 60rpx;
    position: absolute;
    left: 0;
    top: 21rpx;
    background: red;
    z-index: 1;
  }

  .left {
    height: 100%;
    width: 200rpx;
    flex-shrink: 0;
    overflow: auto;
    background: #eee;
  }



  .brand-item .brand-title {
    text-align: center;
    line-height: 2;
  }

  .brand-item .brand-list {
    display: flex;
    flex-wrap: wrap;
  }

  .brand-item .brand {
    width: 33.33%;
    padding: 20rpx;
    box-sizing: border-box;
  }

  .brand-item img {
    width: 100%;
    height: 100rpx;
  }

  .right p {
    line-height: 1.5;
    font-size: 14px;
    text-align: center;
  }

  .right {
    flex: 1;
    height: 100%;
    overflow: auto;
  }
</style>
