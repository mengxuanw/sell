<template>
    <div class="seller" ref="seller">
        <div class="seller-wrapper">
            <div class="overview">
                <h1 class="title">{{seller.name}}</h1>
                <div class="desc">
                    <star :size="36" :score="seller.score" class="star"></star>
                    <span class="ratingCount">({{seller.ratingCount}})</span>
                    <span class="sellCount">月售{{seller.sellCount}}单</span>
                </div>
                <ul class="remark">
                    <li class="block">
                        <h2 class="title">起送价</h2>
                        <div class="content">
                            <span class="stress">{{seller.minPrice}}</span>元
                        </div>
                    </li>
                    <li class="block">
                        <h2 class="title">商家配送</h2>
                        <div class="content">
                            <span class="stress">{{seller.deliveryPrice}}</span>元
                        </div>
                    </li>
                    <li class="block">
                        <h2 class="title">平均配送时间</h2>
                        <div class="content">
                            <span class="stress">{{seller.deliveryTime}}</span>分钟
                        </div>
                    </li>
                </ul>
                <div class="favorite" @click="toggleFavorite">
                    <span class="icon-favorite" :class="{'active':favorite}"></span>
                    <span class="text">{{favoriteText}}</span>
                </div>
            </div>
            <split></split>
        </div>
    </div>
</template>

<script>
import star from '../star/star'
import split from '../split/split'
import {saveToLocal, loadFromLocal} from '../../common/js/store'
import BScroll from 'better-scroll'

export default {
  name: 'seller',
  props: {
    seller: Object
  },
  data () {
    return {
      favorite: (() => {
        return loadFromLocal(this.seller.id, 'favorite', false)
      })()
    }
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  mounted () {
    this.scroll = new BScroll(this.$refs.seller, {
      click: true
    })
  },
  components: {
    star,
    split
  },
  methods: {
    toggleFavorite () {
      if (!event._constructed) {
        return
      }
      this.favorite = !this.favorite
      saveToLocal(this.seller.id, 'favorite', this.favorite)
    }
  }
}
</script>

<style lang="stylus" scoped>
    @import '../../common/stylus/mixin.styl'

    .seller
        position: absolute
        top: 178px
        left: 0
        bottom: 0
        width: 100%
        overflow: hidden
        .seller-wrapper
            .overview
                position: relative
                padding: 18px
                .title
                    margin-bottom: 8px
                    line-height: 14px
                    font-size: 14px
                    color: rgb(7,17,27)
                .desc
                    border-1px(rgba(7,17,27,.1))
                    font-size:0
                    padding-bottom: 18px
                    .star
                        display: inline-block
                        vertical-align: top
                    .ratingCount
                        margin-left: 8px
                        line-height: 18px
                        font-size: 10px
                        color: rgb(77,85,93)
                    .sellCount
                        margin-left: 12px
                        line-height: 18px
                        font-size: 10px
                        color: rgb(77,85,93)
                .remark
                    padding-top: 18px
                    display: flex
                    .block
                        flex: 1
                        border-right: 1px solid rgba(7,17,27,.1)
                        text-align: center
                        &:last-child
                            border-right: none
                        .title
                            margin-bottom: 4px
                            line-height: 10px
                            font-size: 10px
                            color: rgb(147,153,159)
                        .content
                            line-height: 24px
                            font-size: 10px
                            font-weight: 200
                            color: rgb(7,17,27)
                            .stress
                                font-size: 24px
                .favorite
                    position: absolute
                    right: 18px
                    top: 18px
                    width: 50px
                    text-align: center
                    .icon-favorite
                        display: block
                        margin-bottom: 4px
                        line-height: 24px
                        font-size: 24px
                        color: #ccc
                        &.active
                            color: rgb(240,20,20)
                    .text
                        line-height: 10px
                        font-size: 10px
                        color: rgb(77,85,93)
</style>
