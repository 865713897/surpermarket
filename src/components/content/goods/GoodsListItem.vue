<template>
  <div class='goods-item' @click="goToDetail">
    <img :src="showImage" :key="showImage" alt="" @load="imageLoad">
    <div class="goods-info">
      <p>{{goodsItem.title}}</p>
      <span class="price">¥{{goodsItem.price}}</span>
      <span class="collect">{{goodsItem.cfav}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      goodsItem: {
        type: Object,
        default() {
          return {}
        }
      }
    },
    computed: {
      showImage() {
        return this.goodsItem.image || this.goodsItem.show.img || this.goodsItem.img
      }
    },
    methods: {
      goToDetail() {
        // 1.获取iid
        // let iid = this.goods.iid;
        // console.log("跳转");
        this.$router.push('/detail/' + this.goodsItem.iid)
        // 2.跳转到详情页面
        // this.$router.push({path: '/detail', query: {iid}})
      },
      // 监听图片加载
      imageLoad() {

        this.$bus.$emit('itemImageLoad')

        // console.log("hahaha");
        // if(this.$route.path.indexOf('/home')) {
        //   this.$bus.$emit('itemImageLoad')
        // }
        // else if(this.$route.path.indexOf('/detail')) {
        //   this.$bus.$emit('detailItemImageLoad')
        // }
      }
    },
  }
</script>
<style scoped>
  .goods-item {
    padding-bottom: 40px;
    position: relative;
    width: 48%;
  }
  .goods-item img {
    width: 100%;
    border-radius: 5px;
  }

  .goods-info {
    font-size: 12px;
    position: absolute;
    bottom: 5px;
    left: 0;
    right: 0;
    overflow: hidden;
    text-align: center;
  }

  .goods-info p {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    margin-bottom: 3px;
  }

  .goods-info .price {
    color: var(--color-high-text);
    margin-right: 20px;
  }

  .goods-info .collect {
    position: relative;
  }

  .goods-info .collect::before {
    content: '';
    position: absolute;
    left: -15px;
    top: 0;
    width: 14px;
    height: 14px;
    background: url("~assets/img/common/collect.svg") 0 0/14px 14px;
  }
</style>