<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item border-1px">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <!-- 路由匹配到的组件将显示在这里 -->
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import Header from './components/header/Header'
import axios from 'axios'
import {urlParse} from 'common/js/util'

export default {
  name: 'App',
  data () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  components: {
    'v-header': Header
  },
  methods: {
    getInfo () {
      axios.get('/static/mock/seller.json?id=' + this.seller.id)
        .then(this.getInfoSucc)
    },
    getInfoSucc (res) {
      if (res.data) {
        this.seller = Object.assign({}, this.seller, res.data)
      }
    }
  },
  mounted () {
    this.getInfo()
  }
}
</script>

<style lang="stylus" scoped>
  @import './common/stylus/mixin.styl'
  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    .tab-item
      flex: 1
      text-align: center
      border-1px(rgba(7, 17, 27, 0.1))
      & > a
        display: block
        font-size: 14px
        color: rgb(77,85,93)
      a.active
          color: rgb(240,20,20)
</style>
