<template>
  <div class="selelr-wrapper" ref='seller'>
    <div class="seller">
      <div class="seller-info">
        <div class="food-info">
          <div class="left">
            <div class="name">{{seller.name}}</div>
            <div class="star">
              <star :score="seller.score" :size="36"></star>
              <span class="rating-count">({{seller.ratingCount}})</span>
              <span class="sell-count">月售{{seller.sellCount}}单</span>
            </div>
          </div>
          <div class="favorite">
            <favorite @click.native="toggleFav" :text="'收藏'" :favorite="favorite"></favorite>
          </div>
        </div>
        <div class="delivery-info">
          <div class="item delivery-start">
            <div class="text">起送价</div>
            <span class="value">
              <strong class="strong">{{seller.minPrice}}</strong>
              元
            </span>
          </div>
          <div class="item delivery-seller">
            <div class="text">商家配送</div>
            <span class="value">
              <strong class="strong">{{seller.deliveryPrice}}</strong>
              元
            </span>
          </div>
          <div class="item delivery-time">
            <div class="text">平均配送时间</div>
            <span class="value">
              <strong class="strong">{{seller.deliveryTime}}</strong>
              分钟
            </span>
          </div>
        </div>
      </div>
      <split></split>
      <div class="discount-info">
        <div class="bulletin">
          <h2 class="title">公告与活动</h2>
          <p class="description">{{seller.bulletin}}</p>
        </div>
        <div class="detail">
          <p class="decription-p" v-if="seller.supports" v-for="(item,index) in seller.supports" :key="index">
              <span :class="classMap[item.type]"></span>
              {{item.description}}
          </p>
        </div>
      </div>
      <split></split>
      <div class="photos">
        <h1 class="photos-title">商家实景</h1>
        <div class="photoWrapper" ref="photoWrapper">
          <ul class="photo-list" ref="photoList">
            <li class="item" v-for="(item,index) in seller.pics" :key="index">
              <img class="photo" :src="item" >
            </li>
          </ul>
        </div>

      </div>
      <split></split>
      <div class="seller-detail">
        <h1 class="title">商家信息</h1>
        <p class="detail-p" v-for="(item,index) in seller.infos" :key="index">
          {{item}}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import Split from "components/split/split.vue";
import Star from "components/star/star.vue";
import Favorite from "components/favorite/favorite.vue";
import { saveToLocal, loadFromLocal } from "common/js/store";
import Bscroll from "better-scroll";
export default {
  data() {
    return {
      favorite: (() => {
        return loadFromLocal(this.seller.id, "favorite", false);
      })(),
      classMap: [
        "decrease_1",
        "discount_1",
        "guarantee_1",
        "invoice_1",
        "special_1"
      ]
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  watch: {
    seller() {
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    }
  },
  mounted() {
    this.$nextTick(() => {
      this._initScroll();
      this._initPics();
    });
  },
  methods: {
    toggleFav() {
      this.favorite = !this.favorite;
      saveToLocal(this.seller.id, "favorite", this.favorite);
    },
    _initScroll() {
      if (!this.scroll) {
        this.scroll = new Bscroll(this.$refs.seller, {
          click: true
        });
      } else {
        this.scroll.refresh();
      }
    },
    _initPics() {
      if (this.seller.pics) {
        let picWidth = 120;
        let margin = 3;
        let wrapperWidth =
          (margin + picWidth) * this.seller.pics.length
        this.$refs.photoList.style.width = wrapperWidth + "px";
        this.$nextTick(() => {
          if (!this.photoScroll) {
            this.photoScroll = new Bscroll(this.$refs.photoWrapper, {
              click: true,
              scrollX: true,
              eventPassthrough: 'vertival'
            });
          } else {
            this.photoScroll.refresh();
          }
        });
      }
    }
  },
  components: {
    Split,
    Star,
    Favorite
  }
};
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/icon.css';
@import '../../common/stylus/index';

.selelr-wrapper {
  position: absolute;
  top: 190px;
  bottom: 0;
  width: 100%;
  overflow: hidden;

  .seller {
    .seller-info {
      .food-info {
        margin: 10px 15px;
        padding: 10px 0;
        display: flex;
        justify-content: space-between;
        border-bottom: solid 1px rgba(7, 17, 27, 0.1);

        .left {
          .name {
            font-size: 14px;
            line-height: 16px;
          }

          .star {
            display: flex;
            padding: 10px 0;
            font-size: 10px;
            color: gray;
            vertical-align: text-bottom;

            .rating-count {
              padding-right: 10px;
            }

            span {
              line-height: 18px;
            }
          }
        }
      }

      .delivery-info {
        display: flex;
        padding-top: 10px;
        padding-bottom: 20px;
        justify-content: space-around;
        text-align: center;

        .item {
          border-right: solid 1px rgba(7, 17, 27, 0.1);
          padding: 0 24px;

          &:nth-last-of-type(1) {
            border-right: none;
          }

          .text {
            color: gray;
            font-size: 12px;
            line-height: 14px;
          }

          .value {
            font-size: 10px;

            .strong {
              font-size: 24px;
            }
          }
        }
      }
    }

    .discount-info {
      .bulletin {
        padding: 15px 15px 0 15px;

        .title {
          font-size: 14px;
        }

        .description {
          padding: 10px;
          font-size: 10px;
          line-height: 22px;
          color: red;
        }
      }

      .detail {
        text-align: left;
        font-size: 14px;
        margin: 15px;

        .decription-p {
          padding: 10px;
          font-size: 9px;
          line-height: 20px;
          border-top: solid rgba(7, 17, 27, 0.06) 1px;
          vertical-align: middle;

          span {
            width: 16px;
            height: 16px;
            display: inline-block;
            vertical-align: middle;
            background-size: 16px 16px;

            &.decrease_1 {
              bg-image('decrease_4');
            }

            &.discount_1 {
              bg-image('discount_4');
            }

            &.guarantee_1 {
              bg-image('guarantee_4');
            }

            &.invoice_1 {
              bg-image('invoice_4');
            }

            &.special_1 {
              bg-image('special_4');
            }
          }
        }
      }
    }

    .photos {
      margin: 15px;
      overflow: hidden;
      .photos-title {
        font-size: 14px;
        margin-bottom: 10px;
      }
      .photoWrapper{
        width 100%
        .photo-list {
          white-space: nowrap;
          overflow: hidden;
          .item {
            width: 120px;
            height: 90px;
            display: inline-block;
            margin: 0 3px;
            img {
              width: 100%;
              height: 100%;
            }
          }
        }
      }
    }

    .seller-detail {
      font-size: 14px;
      margin: 15px;

      .title {
        padding-bottom: 10px;
      }

      .detail-p {
        padding: 15px 10px;
        border-top: solid 1px rgba(7, 17, 27, 0.1);
        font-size: 10px;
        line-height: 14px;
      }
    }
  }
}
</style>
