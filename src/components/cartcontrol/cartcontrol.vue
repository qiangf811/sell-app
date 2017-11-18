<template lang="html">
  <div class="cartcontrol">
    <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click="decreaseCart"></div>
    <div class="cart-count" v-show="food.count>0">
      {{food.count}}
    </div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>
export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart() {
      if (!this.food.count) {
        this.$set(this.food, 'count', 1)
        this.food.count = 1
      } else {
        this.food.count++
      }
    },
    decreaseCart() {
      if (this.food.count) {
        this.food.count--
      }
    }
  }
}
</script>

<style lang="stylus">
  .cartcontrol
    font-size:0
    .cart-decrease,.cart-add
      display: inline-block
      font-size:24px
      line-height:24px
      padding: 6px
      color:rgb(0,160,220)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      line-height:24px
      padding-top:6px
      text-align:center
      font-size:10px
      color:rgb(147,153,159)
</style>
