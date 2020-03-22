<template>
  <div class='cart-bottom-bar'>
    <div class="check-content">
      <check-button 
              :is-checked="isSelectAll" 
              class="check-button"
              @click.native="checkClick">
      </check-button>
      <span>全选</span>
    </div>

    <div class="price">
      合计: {{totalPrice}}
    </div>

    <div class="calculate" @click="calcClick">
      去计算({{checkLength}})
    </div>
  </div>
</template>

<script>
  import CheckButton from 'components/content/checkButton/CheckButton'

  export default {
    components: {
      CheckButton
    },
    computed: {
      totalPrice() {
        return "￥" + this.$store.state.cartList.filter(item => {
          return item.checked
        }).reduce((preValue, item) => {
          return preValue + item.price * item.count
        }, 0).toFixed(2)
      },
      checkLength() {
        return this.$store.state.cartList.filter(item => item.checked).length
      },
      isSelectAll() {
        // 1.使用filter
        // return !(this.$store.state.cartList.filter(item => !item.checked).length)
        if(this.$store.state.cartList.length === 0) {
          return false
        }
        // 2.使用find()函数
        // return !this.$store.state.cartList.find(item => !item.checked)
        // 3.普通遍历
        let isChecked = false;
        for(let item of this.$store.state.cartList) {
          if(!item.checked) {
            return false
          }
        }
        return true
      }
    },
    methods: {
      checkClick() {
        // 全部选中
        if(this.isSelectAll) {
          this.$store.state.cartList.forEach(item => item.checked = false)
        }
        else {
          this.$store.state.cartList.forEach(item => item.checked = true)
        }
      },
      calcClick() {
        if(!this.isSelectAll) {
          this.$toast.show('请选择购买的商品', 1500)
        }
      }
    },
  }
</script>
<style scoped>
  .cart-bottom-bar {
    position: relative;
    display: flex;

    height: 40px;
    line-height: 40px;
    
    background-color: #eee;

    font-size: 14px;
  }

  .check-content {
    display: flex;
    align-items: center;
    margin-left: 10px;
    width: 60px;
  }

  .check-button {
    width: 20px;
    height: 20px;
    line-height: 20px;
    margin-right: 5px;
  }

  .price {
    margin-left: 20px;
    flex: 1;
  }

  .calculate {
    width: 90px;
    background-color: red;
    color: #fff;
    text-align: center;
    border-radius: 25px;
  }
</style>