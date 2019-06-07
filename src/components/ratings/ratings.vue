<template>
    <div class="rating" ref="ratings">
        <div class="rating-wrapper">
            <div class="overview">
                <div class="overview-left">
                    <div class="score">{{seller.score}}</div>
                    <div class="comp">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <div class="star">
                            <star :score="seller.serviceScore" :size="36"></star>
                        </div>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品质量</span>
                        <div class="star">
                            <star :score="seller.foodScore" :size="36"></star>
                        </div>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">送达时间</span>
                        <span class="deliveryTime">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingSelect @select="changeSlect" @toggle="toggleContent" :ratings="ratings" :ratingDesc="ratingDesc" :selectType="selectType" :onlyContent="onlyContent"></ratingSelect>
            <div class="ratings-content">
                <ul>
                    <li v-for="(rating,index) of ratings" class="rating-item" :key="index" v-show="showRating(rating.rateType,rating.text)">
                        <span class="avatar">
                            <img :src="rating.avatar" width="28" height="28"/>
                        </span>
                        <div class="user">
                            <span class="name">{{rating.username}}</span>
                            <div class="star">
                                <star class="star-content" :score="rating.score" :size="24"></star>
                                <span class="deliveryTime" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
                            </div>
                            <div class="text">{{rating.text}}</div>
                            <div class="recommend">
                                <i class="icon-thumb_up" v-show="rating.rateType===0"></i>
                                <i class="icon-thumb_down" v-show="rating.rateType===1"></i>
                                <span v-for="(food,index) of rating.recommend" :key="index" class="food">{{food}}</span>
                            </div>
                        </div>
                        <div class="time">{{rating.rateTime | formatDate}}</div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import BScroll from 'better-scroll'
import split from '../split/split'
import ratingSelect from '../ratingselect/ratingselect'
import star from '../star/star'
import {formatDate} from '../../common/js/date'

const ALL = 0
const POSITIVE = 1
const NEGATIVE = 2

export default {
  name: 'ratings',
  data () {
    return {
      ratings: [],
      ratingDesc: {
        all: '全部',
        positive: '满意',
        negative: '不满意'
      },
      selectType: ALL,
      onlyContent: true
    }
  },
  props: {
    seller: Object
  },
  created () {
    axios.get('static/mock/ratings.json')
      .then((res) => {
        if (res.data) {
          this.ratings = res.data
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            })
          })
        }
      })
  },
  components: {
    split,
    ratingSelect,
    star
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
    .rating
        position: absolute
        top: 174px
        left: 0
        bottom: 0
        width: 100%
        overflow: hidden
        .rating-wrapper
            .overview
                display: flex
                padding: 18px
                .overview-left
                    flex: 0 0 135px
                    border-right: 1px solid rgba(7,17,27,.2)
                    text-align: center
                    .score
                        line-height: 28px
                        margin-bottom: 6px
                        font-size: 24px
                        color: rgb(255,153,0)
                    .comp
                        line-height: 12px
                        margin-bottom: 8px
                        font-size: 12px
                        color: rgb(7,17,27)
                    .rank
                        line-height: 10px
                        font-size: 10px
                        color: rgb(7,17,27)
                .overview-right
                    flex: 1
                    margin-left: 24px
                    .score-wrapper
                        font-size: 0
                        .title
                            font-size: 12px
                            margin-right: 12px
                            line-height: 18px
                        .star
                            display: inline-block
                            vertical-align: top
                            margin-bottom: 8px
                        .score
                            line-height: 18px
                            font-size: 12px
                            color: rgb(255,153,0)
                        .deliveryTime
                            line-height: 18px
                            font-size: 12px
                            color: rgb(147,153,159)
            .ratings-content
                padding: 0 18px
                .rating-item
                    position: relative
                    padding: 18px 0
                    border-bottom: 1px solid rgba(7,17,27,.1)
                    font-size: 0
                    .avatar
                        position: absolute
                        left: 0
                        top: 18px
                        width: 28px
                        height: 28px
                        img
                            border-radius: 50%
                    .user
                        padding-left: 40px
                        .name
                            line-height: 12px
                            font-size: 10px
                            color: rgb(7,17,27)
                        .star
                            margin-top: 4px
                            font-size: 0
                            .star-content
                                display: inline-block
                            .deliveryTime
                                margin-left: 6px
                                font-size: 10px
                                font-weight: 200
                                color: rgb(147,153,159)
                        .text
                            margin-top: 6px
                            line-height: 18px
                            font-size: 12px
                        .recommend
                            margin-top: 8px
                            .icon-thumb_up
                                font-size: 12px
                                color: rgb(0,160,220)
                            .icon-thumb_down
                                font-size: 12px
                                color: rgb(183,187,191)
                            .food
                                margin-left: 8px
                                padding: 0 6px
                                line-height: 20px
                                text-align: center
                                border: 1px solid rgba(7,17,27,.1)
                                border-radius: 2px
                                font-size: 9px
                                color: rgb(147,153,159)
                    .time
                        position: absolute
                        top: 18px
                        right: 0
                        line-height: 12px
                        font-size: 10px
                        color: rgb(147,153,159)
</style>
