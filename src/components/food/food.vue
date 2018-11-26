<template>
  <div class="food-wrapper" ref="foodWrapper">
    <div class="food-detail">
      <span @click="backToGoods" class="icon-x"></span>
      <div class="food-info">
        <img class="food-img" :src="food.image" alt="">
        <div class="food-name">{{food.name}}</div>
        <div class="food-sellRating">月售{{food.rating}}份，好评率{{food.sellCount}}%</div>
        <div class="food-price">
          <span class="price-text">￥{{food.price}}</span> 
          <addGoods  v-if="food.count" class="active addgoods" :food="food"></addGoods >
          <div @click="addgoods" v-else class="active addToCard">加入购物车</div>
        </div>
      </div>
      <div class="split"></div>
      <div class="food-ratings">
        <h2 class="rating-title">商品评价</h2>
        <div class="recommentOr">
          <ratingSelect :ratingSelectInfo="ratingSelectInfo" @getSelectInfo="getSelectInfo"></ratingSelect>
        </div>
        <div @click="filters.seeContent = !filters.seeContent" class="ratingTextOr" >
          <i class="icon-check-alt" :class="{'active' : filters.seeContent}"></i>
          <span class="text">只看有内容的评价</span>
        </div>
        <div class="rating-list">
          <ul class="ratings">
            <li class="rating-item" v-for="(item,index) in filteredRatings" :key="index">
               <div class="user-info">
                 <span class="rating-time">2016-07-23 18:54</span>
                 <div class="user">
                   <span class="name">{{item.username}}</span>
                   <img class="avatar" :src="item.avatar" alt="">
                 </div>
               </div>
               <div class="rating-content">
                 <i v-if="item.rateType === 0" class="icon-checkmark"></i>
                 <i v-else-if="item.rateType === 1" class="icon-x"></i>
                 <span class="rating-text">{{item.text}}</span>
               </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import AddGoods from 'components/addGoods/addGoods'
  import RatingSelect from 'components/ratingSelect/ratingSelect'
  import Vue from 'vue'
  import Bscroll from 'better-scroll'
  export default {
    props: {
      food : Object
    },
    data(){
      return {
        filters : {
          rateType : '',
          seeContent : false
        }
      }
    }, 
    computed:{
      ratingSelectInfo(){
        let ratings = this.food.ratings
        let ratingSelectInfo = {
              recommendNum : 0,
              notRecommendNum : 0
            }
        if(!ratings || ratings.length <= 0){
          return
        }
        ratingSelectInfo.all = ratings.length
        ratings.forEach(item => {
          if(item.rateType === 0){
            ratingSelectInfo.recommendNum++
          }
        })
        ratings.forEach(item => {
          if(item.rateType === 1){
            ratingSelectInfo.notRecommendNum++
          }
        })
        return ratingSelectInfo
      },
      filteredRatings(){
        let ratings = this.food.ratings
        if(!ratings || ratings.length <= 0){
          return
        }
        if(this.filters.rateType === 1){
          ratings = ratings.filter(item => {
            return item.rateType == 1
          })
        }else if(this.filters.rateType === 0){
          ratings = ratings.filter(item => {
            return item.rateType == 0
          })
        }
        if(this.filters.seeContent == true){
          ratings = ratings.filter(item => {
            return item.text
          })
        }
        return ratings
      }
    },
    methods:{
      getSelectInfo(selectInfo){
        if(selectInfo === "recommend"){
          this.filters.rateType = 0
        }else if (selectInfo === "notRecommend"){
          this.filters.rateType = 1
        }else{
          this.filters.rateType = ""
        }
      },
      addgoods(){
        Vue.set(this.food,'count',1)
      },
      backToGoods(){
        this.$parent.showFood = false
      }
    },
    created(){
      this.$nextTick(() => {
        this.scroll = new Bscroll(this.$refs.foodWrapper,{
          click : true
        })
      })
    },
    components: {
      AddGoods,
      RatingSelect
    }
  }
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/icon.css';
.food-wrapper
  width 100%
  position fixed
  bottom 48px
  top 0px
  overflow hidden
  .food-detail
    .food-ratings
      .rating-title 
        font-size 14px
        margin 15px
      .ratingTextOr 
        margin 15px
        border-top solid rgba(0, 0, 0, 0.1) 1px
        padding-top 15px
        i 
          font-size 24px
          margin-right 4px
          vertical-align middle
          color gray
          &.active
            color #00a0dc
        .text 
          font-size 12px
          color gray
      .recommentOr 
        margin-left 15px
      .rating-list
        border-top solid rgba(0, 0, 0, 0.2) 1px
        .ratings
          margin 15px
          .rating-item
            border-bottom 1px solid rgba(0, 0, 0, 0.1)
            margin 10px 0
            padding-bottom 20px
            .user-info
              display flex
              height 20px
              line-height 20px
              .rating-time
                flex 1
              .user
                .avatar 
                  width 10px
                  height 10px
                  border-radius 50%
                  vertical-align middle
            .rating-content
              .icon-x 
                position relative
                color gray 
                font-size 10px
                padding 0
              .icon-checkmark
                color #00a0dc
    .split
      width 100%
      height 16px
      background #f3f5f7;
      border-top rgba(7,17,27,0.1) 1px solid 
      border-bottom rgba(7,17,27,0.1) 1px solid 
    .food-info
      margin-bottom 20px
      .food-img
        width 100%
      .food-name
        margin 15px
        font-size 14px
        font-weight 700
        line-height 14px
        color #07111b
      .food-sellRating 
        margin-left 15px
        font-size 10px
        color gray 
        margin-bottom 18px
        line-height 10px
        height 10px
      .food-price
        margin-left 15px
        position relative
        .price-text
          color red 
          font-weight 500
        .addgoods
          position absolute
          top -7px
          left 200px
          border-radius 12px
          height 24px
          line-height 24px
        .active
          transition all 0.4
        .addToCard
          position absolute
          top -7px
          left 200px
          background #00a0dc
          border-radius 12px
          height 24px
          line-height 24px
          color white 
          padding 0 12px
    .icon-x
      position fixed
      padding 10px
      color white
      font-size 18px
      display inline-block

</style>
