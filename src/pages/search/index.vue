<template>
  <div>
    <!-- 搜索结果 组 -->
    <div class="search">
      <!-- 搜索框 -->
      <div class="search-input">
        <icon type="search" size="16"></icon>
        <input @input="inputKeywords" v-model="keywords" type="text" placeholder="请输入搜索关键词" confirm-type="search"
          @confirm="searchConfirm">
      </div>
      <!-- 取消按钮 -->
      <button v-if="keywords" class="btn" @click="clearInput">取消</button>
      <!-- 搜索结果 -->
      <div class="search-result" v-if="keywords && getSearchData !== null">
        <ul>
          <li v-show="getSearchData.length === 0" class="result">无搜索结果</li>
          <li :key="item.goods_id" v-for="item in getSearchData">{{item.goods_name}}</li>
        </ul>
      </div>
    </div>
    <!-- 搜索历史 组 -->
    <div class="keywords" v-if="!keywords">
      <div class="keywords-title">
        <h4>搜索记录</h4>
        <icon type="clear" size="16" @click="clearKeyword"></icon>
      </div>
      <ul>
        <!-- 点击搜索结果跳转到 搜索列表页 -->
        <li :key="index" v-for="(item,index) in keywordsListData" @click="clickKeywords(item)">{{item}}</li>
      </ul>
    </div>
  </div>
</template>
<script>
  import request from '../../utils/request'
  export default {
    data() {
      return {
        keywords: '',
        keywordsListData: mpvue.setStorageSync('keyword') || [], // 搜索历史数组
        flag: false,
        getDataOutTime: null, // 定时器
        getSearchData: [], // 搜索结果数据
      }
    },
    created() {
      this.keywords = ""
      this.getSearchData = []
    },
    methods: {
      // input中输入内容触发事件
      inputKeywords() {
        if (this.flag) {
          return
        }
        this.flag = true
        this.getDataOutTime = setTimeout(async () => {
          clearTimeout(this.getDataOutTime)
          const res = await request('goods/qsearch', 'get', {
            query: this.keywords
          })
          const { message } = res.data
          this.getSearchData = message
          this.flag = false
        }, 1000)
      },
      // 清除输入框的内容
      clearInput() {
        this.keywords = ""
      },
      // 点击键盘上的搜索 -- 执行的事件
      async searchConfirm() {
        // 判断输入关键词是否为空
        if (this.keywords !== '' && this.keywords !== ' ') {
          // 判断数组中是否已经有该关键词
          let jde = mpvue.getStorageSync("keyword").indexOf(this.keywords)
          // 如果没有，则插入数组
          if (jde === -1) {
            this.keywordsListData.unshift(this.keywords)
            mpvue.setStorageSync("keyword", this.keywordsListData)
          }
          // 跳转到搜索列表页
          mpvue.navigateTo(
            { url: "/pages/search_list/main?query=" + this.keywords }
          )
        } else {
          return
        }
      },
      // 清除搜索历史
      clearKeyword() {
        mpvue.clearStorageSync("keyword")
        this.keywordsListData = []
      },
      // 搜索历史点击直接跳转到当前关键词对应搜索结果页
      clickKeywords(keywords) {
        mpvue.navigateTo({
          url: '/pages/search_list/main?query=' + keywords
        })
      }
    },
  }
</script>
<style scoped>
  .search {
    width: 100%;
    padding: 10rpx 10rpx;
    display: flex;
    font-size: 28rpx;
    box-sizing: border-box;
    position: relative;
  }

  .search-input {
    display: flex;
    flex: 1;
    border: 2rpx solid #eee;
    border-radius: 10rpx;
    height: 52rpx;
    line-height: 52rpx;
  }

  .search-input input {
    flex: 1;
    padding: 0 18rpx;
  }

  .search-input icon {
    display: block;
    width: 28rpx;
    height: 28rpx;
    padding: 6rpx 0 0 6rpx;
  }

  .btn {
    margin-left: 10rpx;
    width: 120rpx;
    font-size: 22rpx;
    padding: none;
  }

  .search-result {
    position: absolute;
    margin-top: 50rpx;
    padding: 20rpx 30rpx;
    font-size: 30rpx;
    box-sizing: border-box;
    width: 100%;
  }

  .search-result ul {
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  .search-result li {
    height: 50rpx;
    line-height: 50rpx;
  }

  .result {
    height: 100%;
    width: 100%;
    text-align: center;
  }

  .keywords {
    margin-top: 30rpx;
    padding: 20rpx 20rpx;
  }

  .keywords-title {
    height: 50rpx;
  }

  .keywords h4 {
    float: left;
    /* font-weight: 700; */
    font-size: 32rpx;
  }

  .keywords icon {
    float: right;
  }

  .keywords li {
    float: left;
    font-size: 28rpx;
    margin: 10rpx 10rpx 0 0;
    padding: 10rpx 20rpx;
    background-color: #eee;
    border-radius: 10rpx;
  }
</style>
