<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    
    <tab-control :titles="['流行', '新款', '精选']" 
                   ref="tabControl1" 
                   @tabClick="tabClick" class="tab-control" v-show="isTabFixed"></tab-control>

    <Scroll class="content" 
            ref="scroll" 
            :probe-type="3" 
            :pull-up-load="true" 
            @scroll="contentScroll" 
            @pullingUp="loadMore">
      <home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <tab-control :titles="['流行', '新款', '精选']" 
                   ref="tabControl2" 
                   @tabClick="tabClick"></tab-control>
      <feature-view></feature-view>
      <goods-list :goods="showGoods"></goods-list>
    </Scroll>
    
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>

  </div>
</template>

<script>
  import HomeSwiper from './childComps/HomeSwiper'
  import RecommendView from './childComps/RecommendView'
  import FeatureView from './childComps/FeatureView'
  
  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  import {getHomeMultidata, getHomeGoods} from 'network/home'
  // import Swiper from 'components/common/swiper/Swiper'
  // import SwiperItem from 'components/common/swiper/SwiperItem'
  import {debounce} from 'common/utils'

  import BScroll from 'better-scroll'

  export default {
    components: {
      HomeSwiper,
      RecommendView,
      FeatureView,
      NavBar,
      TabControl,
      GoodsList,
      Scroll,
      BackTop
    },
    data() {
      return {
        banners: [],
        recommends: [],
        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []}
        },
        currentType: 'pop',
        isShowBackTop: false,
        tabOffsetTop: 0,
        isTabFixed: false,
        saveY: 0,
        itemImgListener: null
      }
    },
    activated() {
      this.$refs.scroll.scrollTo(0, this.saveY, 0)
      this.$refs.scroll.refresh()
    },
    deactivated() {
      // 1.保存y值
      this.saveY = this.$refs.scroll.getCurrentY()
      // console.log(this.saveY);
      // console.log("dadadsa");

      // 2.取消全局事件的监听
      this.$bus.$off('itemImgLoad', this.itemImgListener)
    },
    created() {
      // 1.请求多个数据
      this.getHomeMultidata()

      // 2.请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new'),
      this.getHomeGoods('sell')
    },
    mounted() {
      // 1.监听item中图片加载完成
      const refresh = debounce(this.$refs.scroll.refresh, 50)

      // 对监听的事件进行保存
      this.itemImgListener = () => {
        // console.log('-----');
        refresh()
      }

      this.$bus.$on('itemImageLoad', this.itemImgListener)
    },
    computed: {
      showGoods() {
        return this.goods[this.currentType].list
      }
    },
    methods: {
      // 网络请求相关方法
      getHomeMultidata() {
        getHomeMultidata().then(res => {
          this.banners = res.data.banner.list
          this.recommends = res.data.recommend.list
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1

          // 完成上拉加载更多
          this.$refs.scroll.finishPullUp()
        })
      },

      // 事件监听相关方法
      tabClick(index) {
        switch(index) {
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
        this.$refs.tabControl1.currentIndex = index
        this.$refs.tabControl2.currentIndex = index
      },
      // back-top
      backClick() {
        this.$refs.scroll.scrollTo(0, 0, 500)
      },
      // 监听scroll
      contentScroll(position) {
        // console.log(position);
        // position.y > 1000
        // 1.判断backtop是否显示
        this.isShowBackTop = position.y < -300 ? true : false

        // 2.决定tabControl是否吸顶(position: fixed)
        this.isTabFixed = (position.y) < -this.tabOffsetTop ? true : false

        // 3.记录position.y到saveY,以解决点击到其他页面
        // 返回当前页面,当前页面状态跳转前一样
        // this.saveY = position.y
      },
      // 监听上拉加载
      loadMore() {
        // console.log("上拉加载更多");
        this.getHomeGoods(this.currentType)
      },
      swiperImageLoad() {
        // 1.获取tabControl的offsetTop
        // 所有的组件都有$el: 用于获取组件中的元素
        // console.log(this.$refs.tabControl2.$el.offsetTop);
        this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop
        // console.log(this.tabOffsetTop);
      }
    },
  }
</script>
<style scoped>
  #home {
    /* padding-top: 44px; */
    /* vh: vueport height 视口 */
    height: 100vh;
    position: relative;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;

    /* position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9; */
  }

  .content {
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }

  .tab-control {
    position: relative;
    z-index: 9;
  }

  /* .content {
    height: calc(100% - 44px);
    overflow: hidden;
    margin-top: 44px;
    margin-bottom: 49px;
  } */
</style>