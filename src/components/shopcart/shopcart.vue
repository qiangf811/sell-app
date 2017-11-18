<template lang="html">
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'hightLight':totalCount>0}">
            <i class="icon-shopping_cart" :class="{'hightLight':totalCount>0}"></i>
          </div>
          <div class="num" v-show="totalCount>0">
            {{totalCount}}
          </div>
        </div>
        <div class="price" :class="{'hightLight':totalPrice>0}">
          ￥{{totalPrice}}元
        </div>
        <div class="desc">
          另需配送费￥{{deliveryPrice}}元
        </div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      payClass: '',
      totalPrice: 0
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return [{
          price: 10,
          count: 1
        }]
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  computed: {
    totalCount() {
      let count = 0
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
        count += food.count
      })
      this.totalPrice = total
      return count
    },
    payDesc() {
      if (this.totalPrice === 0) {
        this.payClass = 'not-enough'
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        var diff = this.minPrice - this.totalPrice
        this.payClass = 'not-enough'
        return `还差￥${diff}元起送`
      } else {
        this.payClass = 'enough'
        return '去结算'
      }
    }
  }
}
</script>

<style lang="stylus">
  .shopcart
    position: fixed
    left: 0
    bottom:0
    z-index: 50
    width: 100%
    height:48px
    .content
      display: flex
      background: #141d27
      font-size:0
      color:rgba(255,255,255,0.4)
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top:-10px
          margin: 0 12px
          padding:6px
          width: 56px
          height:56px
          box-sizing:border-box
          vertical-align: top
          border-radius:50%
          background: #141d27
          .logo
            width: 100%
            height:100%
            border-radius:50%
            text-align:center
            background-color: #2b343c
            &.hightLight
              background-color: rgb(0,160,220)
            .icon-shopping_cart
              line-height:44px
              font-size:24px
              color:#80858a
              &.hightLight
                color:#fff
          .num
            position: absolute
            top:0
            right:0
            width: 24px
            height:16px
            line-height:16px
            text-align:center
            border-radius:16px
            font-size:9px
            font-weight:700
            color:#ffffff
            background-color: rgb(240,20,20)
            box-shadow:0 4px 8px 0 rgab(0,0,0,0.4)
        .price
          display: inline-block
          vertical-align: top
          box-sizing:border-box
          margin-top:12px
          line-height:24px
          padding-right:12px
          border-right:1px solid rgba(255,255,255,0.1)
          font-size:16px
          font-weight:700
          &.hightLight
            color:#fff
        .desc
          display: inline-block
          vertical-align: top
          margin:12px 0 0 12px
          line-height:24px
          font-size:10px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          line-height:48px
          height:48px
          text-align:center
          font-size:12px
          font-weight:700
          &.not-enough
            background: #2b333b
          &.enough
            background: #00b43c
            color:#fff

</style>
