<template>
    <transition name="food">
        <div class="food" v-show="show" ref="food">
            <div class="food-content">
                <div class="image-header">
                    <img :src="food.image"/>
                    <div class="food-back" @click="hideFood">
                        <i class="icon-arrow_lift"></i>
                    </div>
                </div>
                <div class="content">
                    <h1 class="title">{{food.name}}</h1>
                    <div class="extra">
                        <span class="count">月售{{food.sellCount}}份</span>
                        <span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span class="now">￥{{food.price}}</span>
                        <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="addCart">
                        <div class="cartControl-wrapper">
                            <cartControl @clickDrop="addFood" :food="food"></cartControl>
                        </div>
                        <transition name="buy">
                            <div class="add" @click.stop.prevent="addFirst" v-show="food.count===0 || !food.count">加入购物车</div>
                        </transition>
                    </div>
                </div>
                <split v-show="food.info"></split>
                <div class="info" v-show="food.info">
                    <h1 class="title">商品介绍</h1>
                    <p class="content">{{food.info}}</p>
                </div>
                <split v-show="food.info"></split>
                <ratingSelect @select="changeSlect" @toggle="toggleContent" :ratings="food.ratings" :ratingDesc="ratingDesc" :selectType="selectType" :onlyContent="onlyContent"></ratingSelect>
                <div class="ratings-wrapper">
                    <ul>
                        <li v-for="(rating,index) of food.ratings" class="rating-item" :key="index" v-show="showRating(rating.rateType,rating.text)">
                            <div class="time">{{rating.rateTime | formatDate}}</div>
                            <div class="user">
                                <span class="name">{{rating.username}}</span>
                                <div class="avatar">
                                    <img :src="rating.avatar" width="12" height="12"/>
                                </div>
                            </div>
                            <div class="rating-content">
                                <i class="icon-thumb_up" v-show="rating.rateType===0"></i>
                                <i class="icon-thumb_down" v-show="rating.rateType===1"></i>
                                <span class="text">{{rating.text}}</span>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
import BScroll from 'better-scroll'
import cartControl from '../cartControl/cartControl'
import vue from 'vue'
import split from '../split/split'
import ratingSelect from '../ratingselect/ratingselect'
import {formatDate} from '../../common/js/date'

const ALL = 0
const POSITIVE = 1
const NEGATIVE = 2

export default {
  name: 'food',
  props: {
    food: Object
  },
  components: {
    cartControl,
    split,
    ratingSelect
  },
  data () {
    return {
      show: false,
      ratingDesc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      },
      selectType: ALL,
      onlyContent: true
    }
  },
  methods: {
    showRating (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else if (this.selectType === POSITIVE) {
        return type === 0
      } else if (this.selectType === NEGATIVE) {
        return type === 1
      }
    },
    changeSlect (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleContent () {
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    showFood () {
      /* eslint-disable no-unused-expressions */
      this.show = true
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hideFood () {
      this.show = false
    },
    addFirst (event) {
      if (!event._constructed) {
        return
      }
      this.$emit('add', event.target)
      vue.set(this.food, 'count', 1)
    },
    addFood (target) {
      this.$emit('add', target)
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  }
}
</script>

<style lang="stylus" scoped>
    @import '../../common/stylus/mixin.styl'

    .food
        position: fixed
        left: 0
        top: 0
        bottom: 48px
        z-index: 40
        overflow: hidden
        background: #fff
        transform: translate3d(0,0,0)
        &.food-enter, &.food-leave-active
            transform: translate3d(100%,0,0)
        &.food-enter-active, &.food-leave-active
            transition: all .5s
        .image-header
            position: relative
            left: 0
            top: 0
            width: 100%
            height: 0
            padding-bottom: 100%
            img
                position: absolute
                left: 0
                top: 0
                width: 100%
                height: 100%
            .food-back
                position: absolute
                left: 0
                top: 10px
                .icon-arrow_lift
                    padding: 10px
                    color: #fff
        .content
            position: relative
            left: 0
            top: 0
            padding: 18px
            .title
                line-height: 14px
                margin-bottom: 8px
                font-size: 14px
                font-weight: 700
                color: rgb(7,17,27)
            .extra
                font-size: 0
                color: rgb(147,153,159)
                line-height: 10px
                margin-bottom: 12px
                .count
                    font-size: 10px
                    margin-right: 12px
                .rating
                    font-size: 10px
            .price
                .now
                    font-size: 14px
                    color: red
                    font-weight: 700
                    line-height: 24px
                .old
                    font-size: 10px
                    color: rgb(147,153,159)
                    font-weight: 700
                    line-height: 24px
                    text-decoration: line-through
            .add
                position: absolute
                right: 18px
                bottom: 18px
                width: 74px
                height: 24px
                line-height: 24px
                text-align: center
                border-radius: 24px
                font-size: 10px
                background: rgb(0,160,220)
                color: #fff
                opacity: 1
                &.buy-enter, &.buy-leave-to
                    opacity: 0
                &.buy-enter-active, &.buy-leave-active
                    transition: all .4s
            .cartControl-wrapper
                position: absolute
                right: 12px
                bottom: 12px
        .info
            padding: 18px
            .title
                font-size: 14px
                margin-bottom: 6px
            .content
                padding: 8px
                font-size: 12px
                font-weight: 200
                line-height: 24px
                color: rgb(77,85,93)
        .ratings-wrapper
            padding: 0 18px
            .rating-item
                position: relative
                padding: 16px 0
                border-1px(rgba(7,17,27,.1))
                .time
                    line-height: 12px
                    margin-bottom: 6px
                    color: rgb(147,153,159)
                    font-size: 10px
                .user
                    position: absolute
                    right: 0
                    top: 16px
                    line-height: 12px
                    font-size: 0
                    .name
                        font-size: 10px
                        color: rgb(147,153,159)
                    .avatar
                        display: inline-block
                        margin-left: 6px
                        img
                            border-radius: 12px
                .rating-content
                    font-size: 0
                    line-height: 16px
                    .icon-thumb_up
                        font-size: 12px
                        color: rgb(0,160,220)
                    .icon-thumb_down
                        font-size: 12px
                        color: rgb(147,153,159)
                    .text
                        margin-left: 4px
                        font-size: 12px
                        color: rgb(7,17,27)
</style>
