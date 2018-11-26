<template>
  <div class="header">
    <div class="top-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" alt="">
      </div>
      <div class="details">
        <div class="title">
          <span class="brand"></span>
          <span>{{seller.name}}</span>
        </div>
        <div class="delivery">
          {{seller.description}}/{{seller.deliveryTime}}分达
        </div>
        <div class="discounts" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="discountText">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div @click="showMoreInfo" class="discountNum" v-if="seller.supports">
        <span class="num">{{seller.supports.length}}个</span>
        <span class="close-icon icon-close:before"></span>   
      </div>
    </div>
    <div @click="showMoreInfo" class="bulletin">
      <span></span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
    </div>
    <div class="bgImg">
      <img :src="seller.avatar" alt="">
    </div>
    <transition name="fade">
      <div class="moreInfo" v-if="showmoreinfo" ref="detailWrapper">
        <div class="detail-wrapper clearfix" >
          <div class="detail-main">
            <h1 class="title">{{seller.name}}</h1>
            <div class="stars">
              <star :score="seller.score" :size="48"></star>
            </div>
     
            <h2 class="decription-title">
              优惠信息
            </h2>
            <div class="decription">
                <p class="decription-p" v-if="seller.supports" v-for="(item,index) in seller.supports" :key="index">
                    <span :class="classMap[item.type]"></span>
                    {{item.description}}{{item.description}}
                </p>
            </div>
            <h2 class="bulletin-title">商家公告</h2>
            <p class="bulletin-p">{{seller.bulletin}}</p>
          </div>
        </div>
      <div @click="hidemoreinfo" class="close-detail">X</div>
    </div>    
    </transition>
   
  </div>
</template>
<style lang="stylus" scoped>
@import '../../common/stylus/mixin';

.header {
  overflow: hidden;
  position: relative;

  .top-wrapper {
    position: relative;
    font-size: 0;
    background-color: rgba(7, 17, 27, 0.4);
    padding: 24px 12px 18px 24px;

    .avatar {
      display: inline-block;
      border-radius: 2px;

      img {
        width: 64px;
        height: 64px;
      }
    }

    .details {
      display: inline-block;
      vertical-align: top;
      padding: 2px 0 4px 16px;
      color: white;

      .title {
        width: 100%;
        font-size: 16px;
        line-height: 18px;
        font-weight: bold;

        .brand {
          width: 30px;
          height: 18px;
          vertical-align: top;
          background-size: 30px 18px;
          display: inline-block;
          bg-image('brand');
        }
      }

      .delivery {
        font-size: 12px;
        font-weight: 200;
        line-height: 12px;
        padding: 8px 0;
      }

      .discounts {
        padding: 2px 0;
        width: 100%;
        position: relative;
        right: 0;

        .icon {
          width: 12px;
          height: 12px;
          display: inline-block;
          background-size: 12px 12px;
          padding-right: 4px;
          background-repeat: no-repeat;
          vertical-align: top;

          &.decrease_1 {
            bg-image('decrease_1');
          }

          &.discount_1 {
            bg-image('discount_1');
          }

          &.guarantee_1 {
            bg-image('guarantee_1');
          }

          &.invoice_1 {
            bg-image('invoice_1');
          }

          &.special_1 {
            bg-image('special_1');
          }
        }

        .discountText {
          font-size: 10px;
          font-weight: 200;
          line-height: 12px;
        }
      }
    }

    .discountNum {
      position: absolute;
      padding: 7px 8px;
      background-color: rgba(0, 0, 0, 0.2);
      bottom: 18px;
      right: 12px;
      border-radius: 20px;
      display: inline-block;

      .num {
        font-size: 10px;
        font-weight: 200;
        line-height: 12px;
        color: white;
        vertical-align: middle;
      }

      .close-icon {
        width: 10px;
        height: 10px;
        display: inline-block;
        color: white;
        padding-left: 2px;
        vertical-align: middle;
      }
    }
  }

  .bulletin {
    height: 28px;
    width: 100%;
    line-height: 28px;
    overflow: hidden;
    white-space: nowrap;
    background-color: rgba(7, 17, 27, 0.5);
    position: relative;

    .bulletin-text {
      font-size: 10px;
      color: white;
      font-weight: 200;
    }
  }

  .bgImg {
    overflow: hidden;
    position: absolute;
    top: 0;
    left: 0;
    border: solid red 1px;
    width: 100%;
    z-index: -1;

    img {
      width: 100%;
      filter: blur(10px);
    }
  }

  .moreInfo {
    border solid red 1px
    position: fixed;
    left: 0;
    top: 0;
    width 100%
    height: 100%;
    z-index: 100;
    overflow: auto;
    color: white;
    background-color: rgba(7, 17, 27, 0.7);
    &.fade-enter-active,&.fade-leave-active{
      transition all 0.5s
    }
    &.fade-enter,&.fade-leave-to{
      opacity 0
      background-color rgba(7,17,27,0)
    }

    .detail-wrapper {
      min-height: 100%;
      width: 100%;
      .detail-main {
        text-align: center;
        margin-top: 40px;
        width: 100%;
        padding-bottom: 64px;
        width 100%
        .title {
          margin: 0 auto;
        }
        .stars {
          padding 10px
        }
        .decription-title {
          width 84px
          text-align center
          position relative
          margin: 0 auto;
          font-size 14px
          &::before{
            position absolute
            content ''
            left -84px
            display inline-block
            width 84px
            border solid rgba(255,255,255,0.2) 1px
            top 6px
          }
          &::after{
            position absolute
            content ''
            left 84px
            display inline-block
            border solid rgba(255,255,255,0.2) 1px
            width 84px
            top 6px
          }
        }
        .decription {
          display inline-block
          margin 20px 0
          text-align left
          font-size 14px 
          width 228px
          .decription-p{
            margin-bottom 10px
            font-size 9px 
            line-height 20px
            vertical-align middle
            span{
              width 16px
              height 16px
              display inline-block
              vertical-align middle
              background-size 16px 16px
              &.decrease_1 {
                bg-image('decrease_1');
              }

              &.discount_1 {
                bg-image('discount_1');
              }

              &.guarantee_1 {
                bg-image('guarantee_1');
              }

              &.invoice_1 {
                bg-image('invoice_1');
              }

              &.special_1 {
                bg-image('special_1');
              }
                }
              }
        }
        .bulletin-title{
          width 84px
          margin: 0 auto;
          font-size 14px
          position relative
          margin: 0 auto;
          &::before{
            position absolute
            content ''
            left -84px
            display inline-block
            width 84px
            border solid rgba(255,255,255,0.2) 1px
            top 6px
          }
          &::after{
            position absolute
            content ''
            left 84px
            display inline-block
            border solid rgba(255,255,255,0.2) 1px
            width 84px
            top 6px
          }
        }
        .bulletin-p{
          width 228px
          display inline-block
          margin-top 20px
          font-size 9px 
          letter-spacing 1px
          line-height 20px
          opacity 0.9
          text-align left 
        }
        
      }
    }

    .close-detail {
      font-size: 32px;
      position: absolute;
      color: rgba(255, 255, 255, 0.5);
      margin-top: -64px;
      transform: translateX(-50%);
      left: 50%;
    }
  }
}
</style>

<script>
import "../../common/stylus/index.styl";
import Star from "components/star/star";
import Bscroll from 'better-scroll'
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      classMap: [
        "decrease_1",
        "discount_1",
        "guarantee_1",
        "invoice_1",
        "special_1"
      ],
      showmoreinfo: false
    };
  },
  methods: {
    showMoreInfo() {
      this.showmoreinfo = true;
    },
    hidemoreinfo() {
      this.showmoreinfo = false;
    }
  },
  components: {
    star: Star
  }
};
</script>
