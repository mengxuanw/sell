<template>
    <div class="ratings">
       <div class="select">
           <h1 class="title">商品评价</h1>
           <div class="selectType">
               <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{ratingDesc.all}}
               <span>{{ratings.length}}</span></span>
               <span @click="select(1,$event)" class="block positive" :class="{'active':selectType===1}">{{ratingDesc.positive}}
               <span>{{positive.length}}</span></span>
               <span @click="select(2,$event)" class="block negative" :class="{'active':selectType===2}">{{ratingDesc.negative}}
               <span>{{negative.length}}</span></span>
           </div>
       </div>
        <div @click="toggleContent" class="selectContent" :class="{'active':onlyContent}">
            <i class="icon icon-check_circle"></i>
            <span class="content">只看有内容的评价</span>
        </div>
    </div>
</template>

<script>

export default {
  name: 'ratingSelect',
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    ratingDesc: Object,
    selectType: Number,
    onlyContent: Boolean
  },
  methods: {
    select (type, event) {
      if (!event._constructed) {
        return
      }
      this.$emit('select', type)
    },
    toggleContent () {
      if (!event._constructed) {
        return
      }
      this.$emit('toggle')
    }
  },
  computed: {
    positive () {
      return this.ratings.filter((rating) => {
        return rating.rateType === 0
      })
    },
    negative () {
      return this.ratings.filter((rating) => {
        return rating.rateType === 1
      })
    }
  }
}
</script>

<style lang="stylus" scoped>
    .ratings
       width: 100%
       padding: 18px 18px 0 18px
       border-bottom: 1px solid rgba(7,17,27,.1)
       .select
            border-bottom: 1px solid rgba(7,17,27,.1)
            .title
                line-height: 14px
                margin-bottom: 8px
                font-size: 14px
                font-weight: 700
                color: rgb(7,17,27)
            .selectType
                margin: 12px 0 18px 0
                .block
                    display: inline-block
                    width: 50px
                    height: 24px
                    margin-right: 8px
                    font-size: 12px
                    line-height: 24px
                    text-align: center
                    border-radius: 2px
                .positive
                    background: rgba(0,160,220,.2)
                    color: rgb(77,85,93)
                    &.active
                        background: rgb(0,160,220)
                        color: #fff
                .negative
                    background: rgba(77,85,93,.2)
                    &.active
                        background: rgb(77,85,93)
                        color: #ccc
        .selectContent
            margin: 12px 0
            line-height: 24px
            color: rgb(147,153,159)
            &.active
                color: rgb(0,160,220)
            .icon
                font-size: 24px
            .content
                display: inline-block
                vertical-align: top
                font-size: 12px
</style>
