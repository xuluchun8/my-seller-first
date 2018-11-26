<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <div class="menu">
        <div class="menu-item" v-for="(item,index) in goods" @click="selectMenu(index)" :class="{'current' : currentIndex === index}">
          <div class="item-wrapper">
           <span v-if="item.type > 0" class="menu-icon" :class="classMap[item.type]"></span>
           <span class="menu-text">{{item.name}}</span>
          </div>
        </div>
      </div>
    </div>
    <div class="content-wrapper" ref="contentWrapper">
      <ul class="goods-wapper">
        <li class="goods-list food-list-hook" v-for="(item,index) in goods"  > 
          <div class="list-header">
            <h1 class="list-title">{{item.name}}</h1>
          </div>
          <ul>
            <li class="list-good" v-for="(good,i) in item.foods" @click="toShowFood(good)">
              <img class="good-img" :src="good.image" />
              <div class="good-infos">
                <h1 class="good-name">{{good.name}}</h1>
                <p v-if="good.description" class="good-description">{{good.description}}</p>
                <p class="sell-info">
                  <span class="sell-count">月售{{good.sellCount}}份</span>
                  <span>好评率{{good.rating}}份</span>
                </p>
                <div class="good-price">
                  <span class="discount-price">￥{{good.price}}</span>
                  <span v-if="good.oldPrice" class="old-price">￥{{good.oldPrice}}</span>
                </div>
                <div class="addToCard">
                  <addGoods @getSelected = "getSelected" :food="good"></addGoods>
                </div>
              </div>
            </li> 
          </ul>         
        </li>
      </ul>
    </div>
    <shopCard :selectedFoods="selectedFoods" ref="shopcard"></shopCard>
    <div class="food" :class="{active : showFood}">
      <food  :food="food"></food>
    </div>

  </div>
</template>

<script>
import BScroll from "better-scroll";
import ShopCard from "components/shopCard/shopCard";
import addGoods from "components/addGoods/addGoods";
import Food from "components/food/food";
export default {
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    shopCard: ShopCard,
    addGoods: addGoods,
    Food
  },
  data() {
    return {
      listHeight: [],
      goods: [],
      food : {},
      showFood : false,
      classMap: [
        "decrease_1",
        "discount_1",
        "guarantee_1",
        "invoice_1",
        "special_1"
      ],
      scrollY: 0
    };
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectedFoods() {
      let foods = [];
      this.goods.forEach(good => {
        good.foods.forEach(food => {
          if (food.count) {
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  created() {
    this.$http.get("/api/goods").then(res => {
      this.goods = res.data.data
      this.$nextTick(() => {
        this._inintScroll();
        this._calculateHeight();
      });
    });
  },
  methods: {
    hideFood(){
      this.showFood = false
    },
    toShowFood(food){
      this.showFood = true
      this.food = food
    },
    getSelected(el) {
      this._drop(el);
    },
    _drop(target) {
      // 体验优化，异步执行下落动画

      this.$nextTick(() => {
        this.$refs.shopcard.drop(target);
      });
    },
    selectMenu(index) {
      // this.currentIndex = index
      let fooListdom = this.$refs.contentWrapper.getElementsByClassName(
        "food-list-hook"
      );
      let indexDom = fooListdom[index];
      this.contentWrapper.scrollToElement(indexDom, 100);
    },
    _inintScroll() {
      this.menuWrapper = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.contentWrapper = new BScroll(this.$refs.contentWrapper, {
        click: true,
        probeType: 3 //实时监听滚动的位置,可以传给下面的scroll监听事件
      });
      // 获取到滚动的位置
      this.contentWrapper.on("scroll", pos => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight() {
      let height = 0;
      let listdoms = this.$refs.contentWrapper.getElementsByClassName(
        "food-list-hook"
      );
      this.listHeight.push(height);
      for (var i = 0; i < listdoms.length; i++) {
        height += listdoms[i].clientHeight;
        this.listHeight.push(height);
      }
    }
  }
};
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/index';
@import '../../common/stylus/icon.css';
.goods 
  position: absolute;
  top: 188px;
  bottom: 48px;
  width: 100%;
  display: flex;
  .menu-wrapper 
    // background-color #f3f5f7
    width: 80px;
    overflow: hidden;
    .menu-item 
      // display table
      width: 100%;
      // margin 0 12px
      padding-top: 12px;
      line-height: 14px;
      background-color: #f3f5f7;
      &.current 
        background-color: white;
        .menu-text 
          vertical-align: top;
          font-weight: 700;
          font-size: 12px;
          line-height: 14px;
      .item-wrapper 
        display: block;
        margin: 0 12px;
        border-bottom: solid rgba(7, 17, 27, 0.1) 1px;
        padding-bottom: 12px;
        .menu-icon 
          display: inline-block;
          width: 14px;
          height: 14px;
          font-size: 12px;
          background-size: 14px 14px;
          &.decrease_1 
            bg-image('decrease_3');
          &.discount_1 
            bg-image('discount_3');
          &.guarantee_1 
            bg-image('guarantee_3');
          &.invoice_1 
            bg-image('invoice_3');
          &.special_1 
            bg-image('special_3');
        .menu-text 
          vertical-align: top;
          font-weight: 200;
          font-size: 12px;
          line-height: 14px;
  .content-wrapper 
    flex: 1;
    overflow: hidden;
    position: relative;
    .goods-list 
      width: 100%;
      display: inline-block;
      .list-header 
        border-left: 3px solid #d9dde1;
        padding-left: 14px;
        background-color: #f2f5f7;
        .list-title 
          font-size: 12px;
          color: rgb(147, 153, 159);
          line-height: 26px;
      .list-good 
        margin: 18px;
        display: flex;
        padding-bottom: 16px;
        border-1px(rgba(147, 153, 159, 0.2));
        .good-img 
          width: 57px;
          height: 57px;
        .good-infos 
          position: relative;
          padding-left: 10px;
          .good-name 
            font-size: 14px;
            line-height: 14px;
            color: rgb(7, 17, 27);
          .good-description, .sell-info 
            padding-top: 8px;
            font-size: 10px;
            line-height: 10px;
            color: rgb(147, 153, 159);
          .sell-info 
            .sellCount 
              &.first-child 
                padding-left: 12px;
          .good-price 
            font-size 12px
            .discount-price 
              font-size: 12px;
              color: red;
              line-height: 28px;
              font-weight: 700;
              padding-right: 8px;
            .icon 
              width 12px
              height 12px
              border solid red 1px
              display inline-block
              color red 
            .old-price 
              font-size: 10px;
              color: rgb(147, 153, 159);
              line-height: 28px;
              font-weight: 700;
          .addToCard 
            position: absolute;
            left: 70px;
            bottom: 25px;
  .food
    font-size 12px
    position fixed
    left 100%
    right 0
    top 0
    // bottom 0
    width 100%
    // height 100%
    z-index 1000
    border-radius solid red 1px
    overflow auto
    transition all 0.3s
    background-color white
    bottom: 48px
    &.active
      left 0

</style>
