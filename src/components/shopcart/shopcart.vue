<template lang="html">
  <div class="">
    <div class="shopcart">
      <div class="content" @click="toggleList">
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
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
      <div class="ball-container">
        <transition-group name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
          <div class="ball" v-for="(ball,index) in balls" :key="index" v-show="ball.show">
            <div class="inner"></div>
          </div>
        </transition-group>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-head">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li v-for="(food,index) in selectFoods" class="food">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>

    </div>
    <transition name="mask">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>

</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'

export default {
  data() {
    return {
      payClass: '',
      totalPrice: 0,
      balls: [{
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }],
      dropBalls: [],
      fold: true
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
    },
    listShow() {
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  created() {
    this.$root.eventHub.$on('cartcontrolAdd', this.drop)
  },
  beforeDestroy: function() {
    this.$root.eventHub.$off('cartcontrolAdd', this.drop)
  },
  methods: {
    drop(el) {
      for (let i = 0, len = this.balls.length; i < len; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          break
        }
      }
    },
    beforeEnter(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          el.style.transform = `translate3d(0,${y}px,0)`
          let inner = el.getElementsByClassName('inner')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    enter(el) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner')[0]
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
      })
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    toggleList() {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    empty() {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    hideList() {
      this.fold = true
    },
    pay() {
      if (this.totalPrice < this.minPrice) {
        return
      }
    }
  },
  components: {
    cartcontrol
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin.styl'
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
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom:22px
        z-index: 200
        &.drop-enter-active
          transition: all .4s cubic-bezier(0.49,-0.29,0.75,0.41)
          .inner
            width: 16px
            height:16px
            border-radius:50%
            background: rgb(0,160,220)
            transition: all .4s linear
    .shopcart-list
      position: absolute
      top:0
      left:0
      z-index: -1
      width: 100%
      transform:translate3d(0,-100%,0)
      &.fold-enter-active,&.fold-leave-active
        transition:all .5s
      &.fold-enter, &.fold-leave-to
        transform:translate3d(0,0,0)
      .list-head
        height: 40px
        line-height:40px
        padding:0 18px
        background: #f3f5f7
        border-bottom:1px solid rgba(7,17,27,0.1)
        .title
          float:left
          font-size:14px
          color:rgb(7,17,27)
        .empty
          float:right
          font-size:12px
          color:rgb(0,160,220)
      .list-content
        padding:0 18px
        max-height:217px
        overflow: hidden
        background: #ffffff
        .food
          position: relative
          padding:12px 0
          box-sizing:border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height:24px
            font-size:14px
            color:rgb(7,17,27)
          .price
            position: absolute
            right: 90px
            bottom:12px
            line-height:24px
            font-weight:700
            font-size:14px
            color:rgb(240,20,20)
          .cartcontrol-wrapper
            position: absolute
            bottom:6px
            right:0
  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 40
    opacity:1
    background: rgba(7,17,27,0.6)
    backdrop-filter:blur(10)
    &.mask-enter-active,&.mask-leave-active
      transition:all .4s
    &.mask-enter,&.mask-leave-to
      opacity:0
      background: rgba(7,17,27,0)
</style>
