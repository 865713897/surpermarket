<template>
  <div id="detail">
    <detail-nav-bar ref="detailNav" class="detail-nav-bar" @titleClick="titleClick"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :topImages="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detailInfo="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
      <detail-param-info ref="params" :paramInfo="paramInfo"></detail-param-info>
      <detail-comment-info ref="comment" :commentInfo="commentInfo"></detail-comment-info>
      <goods-list ref="recommend" :goods="recommend"></goods-list>
    </scroll>
    <detail-bottom-bar @addToCart="addCart"></detail-bottom-bar>
    <back-top @click.native="backTop" v-show="isShowDetailBackTop"></back-top>

    <!-- <toast :message="message" :show="show"></toast> -->
  </div>
</template>

<script>
  import DetailNavBar from './childComps/DetailNavBar'
  import DetailSwiper from './childComps/DetailSwiper'
  import DetailBaseInfo from './childComps/DetailBaseInfo'
  import DetailShopInfo from './childComps/DetailShopInfo'
  import DetailGoodsInfo from './childComps/DetailGoodsInfo'
  import DetailParamInfo from './childComps/DetailParamInfo'
  import DetailCommentInfo from './childComps/DetailCommentInfo'
  import DetailBottomBar from './childComps/DetailBottomBar'

  import Scroll from 'components/common/scroll/Scroll'
  import GoodsList from 'components/content/goods/GoodsList'
  import BackTop from 'components/content/backTop/BackTop'

  import {
    getDetail, 
    Goods, 
    Shop, 
    GoodsParam, 
    getRecommend
  } from 'network/detail'

  import {debounce} from 'common/utils'

  import {itemListenerMixin} from 'common/mixin'

  import {mapActions} from 'vuex'

  // import Toast from 'components/common/toast/Toast'

  export default {
    name: 'Detail',
    data() {
      return {
        iid: null,
        topImages: [],
        goods: {},
        shop: {},
        detailInfo: {},
        paramInfo: {},
        commentInfo: {},
        recommend: [],
        themeTopYs: [],
        currentIndex: 0,
        isShowDetailBackTop: false,
        // message: '',
        // show: false
      }
    },
    mixins: [itemListenerMixin],
    components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      Scroll,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      GoodsList,
      DetailBottomBar,
      BackTop,
      // Toast
    },
    created() {
      // console.log(this.$route.params.iid);
      // 保存iid
      this.iid = this.$route.params.iid

      // 1.根据iid拿数据,详情数据
      getDetail(this.iid).then(res => {
        // console.log(res);
        const data = res.result
        // 1.获取顶部的轮播图数据
        this.topImages = data.itemInfo.topImages

        // 2.获取商品信息
        this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)
        // console.log(this.goods);

        // 3.创建店铺信息的对象
        this.shop = new Shop(data.shopInfo)

        // 4.保存商品的详情数据
        this.detailInfo = data.detailInfo

        // 5.获取参数信息
        this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

        // 6.获取评论信息
        if(data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0]
        }


        // 将detail-nav-bar中的高度push进数组
        // this.$nextTick(() => {
          // 根据最新的数据,对应的dom已经被渲染出来
          // 但是图片仍然没有加载完成(目前取到的offsetTop值没有包含图片的高度)
          // offsetTop值不对的时候,一般都是图片的加载问题
        //   this.themeTopYs = []

        //   this.themeTopYs.push(0)
        //   this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        //   this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)

        //   console.log(this.themeTopYs);
          
        // })

      })

      // 2.请求推荐数据
      getRecommend().then((res) => {
        // console.log(res);
        this.recommend = res.data.list
      })

      // 3.给getThemeTopYs赋值
      // this.getThemeTopYs = debounce(() => {
      //   this.themeTopYs = []
      //   this.themeTopYs.push(0)
      //   this.themeTopYs.push(this.$refs.params.$el.offsetTop)
      //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
      //   this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)

      //   console.log(this.themeTopYs);
        
      // })

    },

    mounted() {
      
    },

    destroyed() {
      this.$bus.$off('itemImgLoad', this.itemImgListener)
    },

    methods: {

      ...mapActions(['addCart']),

      imageLoad() {
        this.$refs.scroll.refresh()

        // 给themeTopYs赋值
        this.themeTopYs = []
        this.themeTopYs.push(0)
        this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)

        // console.log(this.themeTopYs);
      },
      titleClick(index) {
        // console.log(index);
        this.$refs.scroll.scrollTo(0, -this.themeTopYs[index],500)
      },
      contentScroll(position) {
        // console.log(position);
        // 1.获取y值
        const positionY = -position.y
        
        // 2.positionY和主题中的值进行对比
        let length = this.themeTopYs.length
        for(let i = 0 ; i<length ; i++) {
          // i的类型: String
          if(this.currentIndex !== i && ((i<length-1 && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) || 
            (i === length-1 && positionY >= this.themeTopYs[i]))) {
              // console.log(i);
              this.currentIndex = i
              // console.log(this.currentIndex);
              this.$refs.detailNav.currentIndex = this.currentIndex
          }
        }

        // 3.backTop的显示与回滚
        this.isShowDetailBackTop = positionY > 500 ? true : false

      },
      backTop() {
        this.$refs.scroll.scrollTo(0,0,500)
      },
      addCart() {
        // 1.获取购物车需要展示的信息
        const product = {}
        product.image = this.topImages[0]
        product.title = this.goods.title
        product.desc = this.goods.desc
        product.price = this.goods.realPrice
        product.iid = this.iid

        // 2.将商品添加到购物车
        this.$store.dispatch('addCart', product).then(res => {
          // this.show = true
          // this.message = res
          // setTimeout(() => {
          //   this.show = false
          //   this.message = ''
          // }, 1500)
          // console.log(res);

          this.$toast.show(res, 1500)
        })

        // this.addCart(product).then(res => {
        //   this.show = true
        //   this.message = res
        //   setTimeout(() => {
        //     this.show = false
        //     this.message = ''
        //   }, 1500)
          // console.log(res);
        // })
      }
    },
  }
</script>
<style scoped>
  .detail-nav-bar {
    height: 44px;
    position: relative;
    z-index: 9;
    background-color: #fff;
  }

  #detail {
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }

  .content {
    background-color: #fff;
    height: calc(100% - 44px - 49px);
    overflow: hidden;
  }

</style>