<template>
  <div class="ratings-wrapper" ref="ratingsWrapper">
    <div class="ratings" ref="ratings">
      <div class="scoreInfo">
        <div class="score-left">
          <p class="score">{{seller.score}}</p>
          <p class="text">综合评分</p>
          <p class="highPer">高于周边商家{{seller.rankRate}}%</p>
        </div>
        <div class="score-right">
          <div class="service">
            <span >服务态度</span>
            <star :score="seller.score" :size="36"></star>
            <span class="service-score">{{seller.score}}</span>
          </div>
          <div class="foodScore">
            <span >商品评分</span>
            <star :score="seller.serviceScore" :size="36"></star>
            <span class="foodScore-score">{{seller.serviceScore}}</span>
          </div>
          <div class="deliveryTime">
            <span >送达时间</span>
            <span class="deliveryTime-score">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <div class="ratingInfos">
        <div class="ratingType">
          
        </div>
        <div class="ratingOrNot" @click="showRatingItems">
          <span :class="{active : showratingitems}" class="icon-check-alt"></span>
          <span class="text">只看有内容的评价</span>
        </div>
        <div class="ratingDetail">
          <div class="ratingDetail-item" v-for="(item,index) in newRatings" :key="index">
            <div class="avatar">
              <img :src="item.avatar" alt="">
            </div>
            <div class="infos">
              <div class="name">
                <span class="username">{{item.username}}</span>
                <span class="rateTime">{{item.rateTime}}</span>
              </div>
              <div class="score">
                <star :size="36" :score="item.score" class="star"></star>
                <span v-if="item.deliveryTime">{{item.deliveryTime}}分钟送达</span>
              </div>
              <div class="description">
                {{item.text}}
              </div>
              <div class="recommend" v-if="item.recommend.length > 0">
                <span class="icon-play"></span>
                <span class="icon-play"></span>
                <span class="tag"  v-for="(k,j) in item.recommend" :key="j">{{k}}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="stylus" scoped>
@import '../../common/stylus/icon.css';
.ratings-wrapper
  position: absolute;
  top: 188px;
  bottom: 0px;
  width: 100%;
  overflow hidden
  .ratings 
    border solid white 1px
    .scoreInfo 
      margin: 18px auto;
      display: flex;
      text-align: center;
      .score-right 
        padding-left: 8px;
        .service, .foodScore, .deliveryTime 
          margin-bottom: 5px;
          &.last-child 
            margin-bottom: 0px;
          display: flex;
          .service-score, .foodScore-score 
            color: #f90;
          .deliveryTime-score 
            color: #93999f;
          span 
            font-size: 12px;
            line-height: 18px;
            display: inline-block;
            margin-right: 6px;
      .score-left 
        border-right: solid rgba(7, 17, 27, 0.1) 1px;
        width: 120px;
        .score 
          font-size: 24px;
          color: rgb(255, 153, 0);
          line-height: 28px;
        .text 
          font-size: 12px;
          padding: 4px 0;
          color: rgb(7, 17, 27);
          line-height: 14px;
        .highPer 
          font-size: 10px;
          padding: 2px 0 6px 0;
          color: rgba(7, 17, 27, 0.6);
          line-height: 10px;
    .ratingInfos 
      width 100%
      
      .ratingOrNot 
        border-bottom: 1px solid rgba(7, 17, 27, 0.1);
        color: #93999f;
        line-height: 24px;
        padding: 12px 18px;
        .icon-check-alt 
          vertical-align: middle;
          font-size: 24px;
          &.active
            color green
        .text 
          font-size: 12px;
      .ratingDetail 
        width 100%
        .ratingDetail-item 
          display flex
          margin 12px 
          padding-bottom 18px
          border-bottom solid rgba(7,17,27,0.1) 1px
          .avatar
            // margin 12px
            flex: 0 0 28px
            width 28px
            height 24px
            display inline-block
            img 
              border-radius 50%
              width 100% 
          .infos
            flex 1
            vertical-align top
            padding-left  12px
            .name
              font-size 12px
              .username
                color rgb(7,17,27)
                line-height 24px
              .rateTime
                float right
                color rgb(147,153,159)
                line-height 24px
            .score
              .star
                display inline-block
                vertical-align top
                line-height 18px
              span
                font-size 12px
                // vertical-align middle
            .description
              color rga(7,17,27)
              line-height 18px
              font-size 12px
              margin-bottom 4px
            .recommend
              font-size 12px   
                    
              .icon-play
                color blue
              .tag
                border solid rgba(7,17,27,0.1) 1px
                padding 2px
                margin 2px  
                color #93999f
                margin-left 8px
                word-wrap wrap
                display inline-block
          
</style>

<script>
import Star from 'components/star/star'
import BScroll from 'better-scroll'
import Split from 'components/split/split'
export default {
  computed : {
    newRatings(){
      return this.ratings.map(item => {
        let date = new Date(item.rateTime)
        let y = date.getFullYear()
        let m = date.getMonth() + 1
        let d = date.getDate()
        item.rateTime = y + "-" + (m < 10 ? "0" + m : m) + "-" + (d < 10 ? "0" + d : d) + " " + date.toTimeString().substr(0, 8);
        return item
      })
    },
  },
  components : {
    star : Star,
    Split
  },
  props: {
    seller: {
      type: Object
    }
  },
  data(){
    return {
      ratings : [],
      showratingitems : false,
      $ratings : []
    }
  },
  created() {
    this.$http.get("/api/ratings").then(res => {
      this.ratings = res.data.data
      this.$ratings = res.data.data
      this.$nextTick(() => {
        this.ratingScroll = new BScroll(this.$refs.ratingsWrapper,{
          click : true
        })
      })
    });
  },
  methods: {
    showRatingItems(){
      this.showratingitems = !this.showratingitems
      const storeDate = this.ratings
      if(this.showratingitems){
        this.$nextTick(() => {
        this.ratingScroll.refresh();
        })
        this.ratings = this.ratings.filter(item => {
          return item.text
        })
      }else{
        this.ratings = this.$ratings
      }
    },
    getdate(time) {
      let tiem = new Date(time)
  　 　y = time.getFullYear(),
   　　m = time.getMonth() + 1,
   　　d = time.getDate();
   　　return y + "-" + (m < 10 ? "0" + m : m) + "-" + (d < 10 ? "0" + d : d) + " " + now.toTimeString().substr(0, 8);
    }
  }
};
</script>