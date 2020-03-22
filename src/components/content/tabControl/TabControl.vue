<template>
  <div class="tab-control">
    <div v-for="(item, index) in titles" 
          class="tab-control-item" 
          :class="{active: index === currentIndex}" 
          @click="tabClick(index)">
      <span>{{item}}</span>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      titles: {
        type: Array,
        default() {
          return []
        }
      }
    },
    data() {
      return {
        currentIndex: 0
      }
    },
    methods: {
      tabClick(index) {
        this.currentIndex = index
        this.$emit('tabClick', index)
      }
    },
  }
</script>
<style scoped>
  .tab-control {
    display: flex;
    font-size: 15px;
    text-align: center;
    height: 40px;
    line-height: 40px;
    background-color: #fff;
  }

  .tab-control-item {
    flex: 1;
  }

  .active {
    color: var(--color-high-text);
  }

  .active span {
    padding: 5px;
    border-bottom: 2px solid var(--color-tint);
  }
</style>