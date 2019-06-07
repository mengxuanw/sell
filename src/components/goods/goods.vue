<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul class="menu">
                <li v-for="(item, index) of goods" class="menu-item" :key="index" @click="handleClickMenu(index, $event)" :class="{'current':currentIndex===index}" ref="menuList">
                    <span class="text border-1px">
                        <span v-if="item.type > 0" class="icon" :class="classMap[item.type]"></span>
                        {{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="food-wrapper" ref="foodWrapper">
            <ul>
                <li v-for="(item, index) of goods" class="food-list" :key="index" ref="foodList">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li v-for="(food, index) of item.foods" class="food-item border-1px" :key="index" @click="showFood(food,$event)">
                            <div class="icon">
                                <img :src="food.icon"/>
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="description">{{food.description}}<p>
                                <div class="extra">
                                    <span class="count">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now">￥{{food.price}}</span>
                                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartControl-wrapper">
                                    <cartControl @clickDrop="drop" :food="food"></cartControl>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
        <food @add="drop" ref="foodContent" :food="selectedFood"></food>
    </div>
</template>

<script>
import axios from 'axios'
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'
import cartControl from '../cartControl/cartControl'
import food from '../food/food'

export default {
  name: 'goods',
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  props: {
    seller: Object
  },
  components: {
    shopcart,
    cartControl,
    food
  },
  mounted () {
    axios.get('static/mock/goods.json')
      .then((res) => {
        if (res.data) {
          this.goods = res.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
  },
  created () {
    this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          this._followScroll(i)
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let selectfoods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            selectfoods.push(food)
          }
        })
      })
      return selectfoods
    }
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodScroll = new BScroll(this.$refs.foodWrapper, {
        click: true,
        probeType: 3
      })
      this.foodScroll.on('scroll', (pos) => {
        if (pos.y <= 0) {
          this.scrollY = Math.abs(Math.round(pos.y))
        }
      })
    },
    _calculateHeight () {
      let foodList = this.$refs.foodList
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        height += foodList[i].clientHeight
        this.listHeight.push(height)
      }
    },
    _followScroll (index) {
      let menuList = this.$refs.menuList
      this.menuScroll.scrollToElement(menuList[index], 300, 0, -100)
    },
    handleClickMenu (index, $event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.foodList
      this.foodScroll.scrollToElement(foodList[index])
    },
    drop (el) {
      this.$refs.shopcart.drop(el)
    },
    showFood (food, event) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.foodContent.showFood()
    }
  }
}
</script>

<style lang="stylus" scoped>
    @import  '../../common/stylus/mixin.styl'
    .goods
        position: absolute
        top: 174px
        bottom: 56px
        width: 100%
        display: flex
        overflow: hidden
        .menu-wrapper
            width: 80px
            background: #f3f5f7
            .menu
                .menu-item
                    display: table
                    height: 54px
                    width: 56px
                    padding: 0 12px
                    font-weight: 200
                    &.current
                        position: relative
                        z-index: 10
                        margin-top: -1px
                        font-weight: 700
                        background: #fff
                        .text
                            border-none()
                    .text
                        display: table-cell
                        width: 56px
                        border-1px(rgba(7,17,27,.1))
                        height: 100%
                        vertical-align: middle
                        font-size: 12px
                        line-height: 14px
                        .icon
                            display: inline-block
                            vertical-align: top
                            width: 12px
                            height: 12px
                            background-size: 12px 12px
                            background-repeat: no-repeat
                        .decrease
                            bg-image('decrease_3')
                        .discount
                            bg-image('discount_3')
                        .guarantee
                            bg-image('guarantee_3')
                        .invoice
                            bg-image('invoice_3')
                        .special
                            bg-image('special_3')
        .food-wrapper
            flex: 1
            .title
                height: 26px
                font-size: 12px
                line-height: 26px
                padding-left: 14px
                background: #f3f5f7
                color: rgb(147,153,159)
                border-left: 2px solid #d9dde1
            .food-item
                display: flex
                margin-left: 18px
                border-1px(rgba(7,17,27,.1))
                &:last-child
                    border-none()
                    padding-bottom: 18px
                .icon
                    flex: 0 0 57px
                    margin: 18px 0
                .content
                    flex: 1
                    margin-top: 20px
                    margin-left: 10px
                    .name
                        font-size: 14px
                        color: rgb(7,17,27)
                        line-height: 14px
                        margin-bottom: 8px
                    .description, .extra
                        font-size: 10px
                        color: rgb(147,153,159)
                        margin-bottom: 8px
                    .description
                        line-height: 12px
                    .extra
                        line-height: 10px
                        .count
                            margin-right: 12px
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
                    .cartControl-wrapper
                        position: absolute
                        right: 0
                        bottom: 12px
</style>
