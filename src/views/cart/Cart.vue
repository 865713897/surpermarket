<template>
  <div class="cart">
    <!-- 导航 -->
    <nav-bar class="nav-bar">
      <div slot="center">购物车({{cartLength}})</div>
    </nav-bar>

    <!-- 商品的列表 -->
    <cart-list></cart-list>

    <!-- 底部汇总 -->
    <cart-bottom-bar></cart-bottom-bar>

  </div>
</template>

<script>
  import NavBar from 'components/common/navbar/NavBar'
  
  import CartList from './childComps/CartList'
  import CartBottomBar from './childComps/CartBottomBar'

  import {mapGetters} from 'vuex'

  export default {
    components: {
      NavBar,
      CartList,
      CartBottomBar
    },
    data() {
      return {

      }
    },
    computed: {
      // cartLength() {
      //   return this.$store.getters.cartLength
      // }
      // 两种语法
      ...mapGetters(['cartLength'])

      // ...mapGetters({
      //   length: 'cartLength',
      //   list: 'cartList'
      // })
    },
    methods: {

    },
  }
</script>
<style scoped>
  .cart {
    height: 100vh;
  }

  .nav-bar {
    background-color: var(--color-tint);
    color: white;
  }
</style>