<template>
  <div>
    商品详情
  </div>
</template>
<script>
  import request from '../../utils/request';
  export default {
    data() {
      return {

      }
    },
    async onLoad(qurest) {
      const { goods_id } = qurest
      const res = await request('goods/detail', 'get', {
        goods_id
      })
      console.log(res);

    }
  }
</script>
