<template>
  <div>
    <div class="shopcart">
        <div class="content">
            <div class="content-left">
                <div class="logo-wrapper" @click="handleClickCart">
                    <div class="logo" :class="{'highlight':totalCount>0}">
                        <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
                    </div>
                    <div class="num" v-show='totalCount>0'>{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
                <div class="desc">另需{{deliveryPrice}}元起送</div>
            </div>
            <div class="content-right" @click.stop.prevent="pay">
                <div class="pay" :class="{'enough':totalPrice >= minPrice}">{{payDesc}}</div>
            </div>
        </div>
        <div class="ball-container">
          <div v-for="(ball,index) in balls" :key="index">
            <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
              <div class="ball" v-show="ball.show">
                <div class="inner inner-hook"></div>
              </div>
            </transition>
          </div>
        </div>
        <transition name="cart">
          <div class="shopcart-list" v-show="listShow">
            <div class="header border-1px">
              <h1 class="title">购物车</h1>
              <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list" ref="listContent">
              <ul>
                <li v-for="(food,index) of selectFoods" :key="index" class="food">
                  <span class="name">{{food.name}}</span>
                  <div class="price"><span style="font-size:10px">￥</span>{{food.count * food.price}}</div>
                  <div class="cartControl-wrapper">
                    <cartControl @clickDrop="addFood" :food="food"></cartControl>
                  </div>
                </li>
              </ul>
            </div>
          </div>
        </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="fadeList"></div>
    </transition>
  </div>
</template>

<script>
import cartControl from '../cartControl/cartControl'
import BScroll from 'better-scroll'

export default {
  name: 'shopcart',
  components: {
    cartControl
  },
  data () {
    return {
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: true,
      listShow: false
    }
  },
  props: {
    deliveryPrice: Number,
    minPrice: Number,
    selectFoods: {
      type: Array,
      default () {
        return [
          {}
        ]
      }
    }
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    handleClickCart () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    beforeDrop (el) {
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
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    dropping (el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
        el.addEventListener('transitionend', done)
      })
    },
    afterDrop (el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    addFood () {
      this.drop(event.target)
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
      this.fold = true
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      alert(`支付${this.totalPrice}元`)
    },
    fadeList () {
      this.fold = true
    }
  },
  computed: {
    totalCount () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.count
      })
      return total
    },
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        return `还差${this.minPrice - this.totalPrice}元起送`
      } else {
        return '去结算'
      }
    }
  },
  watch: {
    totalCount () {
      if (!this.totalCount) {
        this.fold = true
      }
    },
    fold () {
      /* eslint-disable no-unused-expressions */
      this.listShow = !this.fold
      if (this.listShow) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh
          }
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
    @import "../../common/stylus/mixin.styl"
    .shopcart
        position: fixed
        left: 0
        bottom: 0
        height: 48px
        width: 100%
        z-index: 50
        .content
            display: flex
            background: #141d27
            .content-left
                flex: 1
                font-size: 0
                .logo-wrapper
                    display: inline-block
                    position: relative
                    top: -10px
                    left: 18px
                    width: 56px
                    height: 56px
                    padding: 6px
                    border-radius: 50%
                    box-sizing: border-box
                    background: #141d27
                    .logo
                        background: #2b343c
                        width: 100%
                        height: 100%
                        text-align: center
                        border-radius: 50%
                        &.highlight
                            background: rgb(0,160,220)
                        .icon-shopping_cart
                            font-size: 24px
                            line-height: 44px
                            color: #80858a
                            &.highlight
                                color: #fff
                    .num
                       position: absolute
                       top: 0
                       left: 30px
                       width: 24px
                       height: 12px
                       line-height: 12px
                       text-align: center
                       border-radius: 12px
                       font-size: 9px
                       font-weight: 700
                       background: rgb(240,20,20)
                       box-shadow: 0px 4px 8px 0px rgba(0,0,0,.4)
                       color: #fff
                .price
                    display: inline-block
                    margin: 16px 0 0 18px
                    vertical-align: top
                    padding-right: 12px
                    font-size: 16px
                    font-weight: 700
                    border-right: 1px solid rgba(255,255,255,.1)
                    color: rgba(255,255,255,.4)
                    &.highlight
                        color: #fff
                .desc
                    display: inline-block
                    margin-left: 12px
                    vertical-align: top
                    margin-top: 18px
                    font-size: 10px
                    color: rgba(255,255,255,.4)
            .content-right
                flex: 0 0 105px
                width: 105px
                .pay
                    height: 48px
                    line-height: 48px
                    text-align: center
                    font-size: 12px
                    font-weight: 700
                    background: #2b333b
                    color: rgba(255,255,255,.4)
                .enough
                    background: rgb(0,160,220)
                    color: #fff
        .ball-container
          .ball
            position: fixed
            left: 32px
            bottom: 22px
            z-index: 200
            transition: all .4s cubic-bezier(0.49,-0.29,0.75,0.41)
            .inner
              width: 16px
              height: 16px
              border-radius: 50%
              background: rgb(0, 160, 220)
              transition: all .4s, linear
        .shopcart-list
          position: absolute
          z-index: -1
          left: 0
          top: 0
          width: 100%
          transform: translate3d(0,-100%,0)
          &.cart-enter, &.cart-leave-to
            transform: translate3d(0,0,0)
          &.cart-enter-active, &.cart-leave-active
            transition: all .5s
          .header
            display: flex
            justify-content: space-between
            width: 100%
            height: 40px
            line-height: 40px
            background: #f3f5f7
            border-1px(rgba(7,17,27,.1))
            .title
              margin-left: 14px
              font-size: 14px
              font-weight: 200
              color: rgb(7,17,27)
            .empty
              margin-right: 18px
              font-size: 12px
              color: rgb(0,160,220)
          .list
            width: 100%
            overflow: hidden
            max-height: 217px
            background: #fff
            .food
              position: relative
              width: 100%
              height: 48px
              border-1px(rgba(7,17,27,.1))
              .name
                display: inline-block
                margin-left: 18px
                line-height: 24px
                padding: 12px 0
                font-size: 14px
                color: rgb(7,17,27)
              .price
                display: inline-block
                position: absolute
                right: 112px
                bottom: 12px
                font-size: 14px
                font-weight: 700
                line-height: 24px
                color: rgb(240,20,20)
              .cartControl-wrapper
                display: inline-block
                position: absolute
                right: 6px
                padding: 6px
    .list-mask
      position: fixed
      left: 0
      top: 0
      width: 100%
      height: 100%
      z-index: 40
      background: rgba(7,17,27,.6)
      backdrop-filter: blur(10px)
      opacity: 1
      &.fade-enter, &.fade-leave-to
        opacity: 0
        background: rgba(7,17,27,0)
      &.fade-enter-active, &.fade-leave-active
        transition: all .5s
</style>
