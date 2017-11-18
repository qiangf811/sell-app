<template lang="html">
  <div class="cartcontrol">
    <transition name="move">
        <div class="cart-decrease" v-show="food.count>0" @click="decreaseCart">
          <span class="inner icon-remove_circle_outline"></span>
        </div>
    </transition>
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
    .cart-decrease
      display: inline-block
      padding: 6px
      opacity:1
      .inner
        display: inline-block
        font-size:24px
        line-height:24px
        color:rgb(0,160,220)
      &.move-enter-active,&.move-leave-active
        transition: all .4s linear
        .inner
          transition: all .2s linear
          ransform:rotate(0)
      &.move-enter, &.move-leave-to
        opacity:0
        transform:translate3d(24px,0,0)
        .inner
          transform:rotate(180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      line-height:24px
      padding-top:6px
      text-align:center
      font-size:10px
      color:rgb(147,153,159)
    .cart-add
      display: inline-block
      font-size:24px
      line-height:24px
      padding: 6px
      color:rgb(0,160,220)
</style>
