<template>
  <!-- ref/children  -->
  <div class='wrapper' ref="wrapper">
    <div class="content">
      <slot>
      
      </slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'

  export default {
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      pullUpLoad: {
        type: Boolean,
        default: false
      }
    },

    data() {
      return {
        scroll: null
      }
    },
    mounted() {
      // 创建better-scroll对象
      this.scroll = new BScroll(this.$refs.wrapper, {
        click: true,
        probeType: this.probeType,
        pullUpLoad: this.pullUpLoad
      })

      // 监听滚动的位置
      if(this.probeType === 2 || this.probeType === 3) {
        this.scroll.on('scroll', (position) => {
        // console.log(position);
        this.$emit('scroll', position)
      })
      }

      // 监听上拉事件:scroll滚动到底部
      if(this.pullUpLoad) {
        this.scroll.on('pullingUp', () => {
        // console.log("上拉加载更多");
        this.$emit('pullingUp')
      })
      }
    },
    methods: {
      scrollTo(x, y, time=500) {
        this.scroll && this.scroll.scrollTo(x, y, time)
      },
      // 完成加载更多
      finishPullUp() {
        this.scroll.finishPullUp()
      },
      // 刷新
      refresh() {
        this.scroll && this.scroll.refresh()
      },

      // 得到y值
      getCurrentY() {
        return this.scroll.y ? this.scroll.y : 0
      }
    },
  }
</script>
<style scoped>

</style>