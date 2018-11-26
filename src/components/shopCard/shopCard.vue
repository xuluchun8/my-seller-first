<template>
  <div class="shopCard">
    <div class="card-wrapper"  @click="toggoleclickCard">
      <div class="card-icon" :class="{ 'showtotalGoods' : totalGoods}">
        <span class="icon-play"></span>
      </div>
      <span v-if="totalGoods" class="goodsNum">{{totalGoods}}</span>
      <span class="total-price" :class="{'showTotalPrice' : totalPrice}" >
        ￥{{totalPrice}}
      </span>
    </div>
    <div v-if="totalPrice < 20" class="accounts delivery-price">
        另需配送费{{20-totalPrice}}元
      </div>
      <div v-else class="accounts" @click="pay">
        去结算
    </div>
    <div class="ball-container">
      <div v-for="ball in balls">
        <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook"></div>
          </div>
        </transition>
      </div>
    </div>
    <transition name="fade">
      <div class="selectedFoodsList" v-show="showSelectedFoodsList">
        <div class="header" v-show="totalGoods">
          <span class="header-text">购物车</span>
          <span class="close" @click="clearSelectedFoods">清空</span>
        </div>
        <div class="wrapper" ref="selectedFoodsListWrapper">
          <div class="content">
            <div class="listWrapper" ref="listWrapper" v-for="(item,index) in selectedFoods" :key="index"> 
              <div class="list-left">
                <div class="name">{{item.name}}</div>
                <div class="price">￥{{item.price}}</div>
              </div>
              <addGoods class="addGoods" @getSelected = "getSelected" :food="item"></addGoods>
            </div>
          </div>
        </div>
      </div>
    </transition>
    <transition name="fadeMask">
      <div class="mask" v-show="showSelectedFoodsList" @click="hideMask">
      </div>
    </transition>
  </div>
</template>

<script>
  import addGoods from 'components/addGoods/addGoods'
  import Vue from 'vue'
  import Bscroll from 'better-scroll'
  export default {
    props:{
      selectedFoods : Array
    },
    data(){
      return {
        fold :true,
        balls : [
          {
            show : false
          },
          {
            show : false
          },
          {
            show : false
          },
          {
            show : false
          },
          {
            show : false
          }
        ],
        dropBalls: []
      }
    },
    computed : {
      showSelectedFoodsList(){
        if(!this.totalGoods){
          this.fold = true
          return false
        }
        let show = !this.fold
        return show 
      },
      totalGoods(){
        let counts = this.selectedFoods.map(item => {
          return item.count
        })
        if(counts.length == 0){
          return 0
        }
        return counts.reduce((total,item) => {
          return total + item
        })

      },
      
      totalPrice(){
        let arr = this.selectedFoods.map(item => {
          return item.count * item.price
        })
        if(arr.length == 0){
          return 0
        }
        let total =  arr.reduce((total,item) => {
          return total + item
        })

        if(total == 0){
          this.showSelectedFoodsList = false
        }
        return total
      }
    },
    components: {
      addGoods : addGoods
    },
    methods: {
      pay(){
        alert('你已付款' + this.totalPrice  + "元")
      },
      hideMask(){
        this.fold = true
      },
      clearSelectedFoods(){
         this.selectedFoods.forEach(food => {
           food.count = 0
         })
      },
      toggoleclickCard(){  
        // if(this.totalGoods > 0){
          this.fold = this.fold == true ? false : true
          this.$nextTick(() => {
            this.scroll = new Bscroll(this.$refs.selectedFoodsListWrapper,{
              click : true
            })
          })
        // }
      },
      getSelected({selectedFood,el}){
        this.selectedFoods.push(selectedFood)
      },
      drop(el){
        for(let i = 0; i < this.balls.length;i++){
          let ball = this.balls[i]
          if(!ball.show){
            ball.show = true;
            ball.el = el
            this.dropBalls.push(ball)
            return
          }
        }
      },
      beforeDrop(el){
        let count = this.balls.length 
        while(count--){
          let ball = this.balls[count]
          if(ball.show){
            let rect = ball.el.getBoundingClientRect()
            let x = rect.left - 45
            let y = -(window.innerHeight - rect.top - 20)
            el.style.display = ''
            el.style.transform = `translate3d(0,${y}px,0`
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.transform = `translate3d(${x}px,0,0)`
          }
        }
      },
      dropping(el,done){
        let rf = el.offsetHeight
        this.$nextTick(() => {
          el.style.transform = 'translate3d(0,0,0)'
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.transform = `translate3d(0,0,0)`
          el.addEventListener('transitionend',done)
        })
      },
      afterDrop(el){
        let ball = this.dropBalls.shift()
        if(ball){
          ball.show = false
          el.style.display = "none"
        }
      }
    }
  }
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/icon.css';
.shopCard {
  position: fixed;
  bottom: 0;
  height: 48px;
  width: 100%;
  background-color: #141d27;
  z-index: 2000;

  .mask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 40;
    opacity: 1;
    background: rgba(7, 17, 27, 0.6);
    &.fadeMask-enter-active, &.fadeMask-leave-active{
      transition: all 0.5s
    }
    &.fadeMask-enter, &.fadeMask-leave-active{
      opacity: 0
      background: rgba(7, 17, 27, 0)
    }  
  }

  .selectedFoodsList {
    background: white;
    z-index: 100;
    width: 100%;
    position: absolute;
    bottom: 48px;
    &.fade-enter-active, &.fade-leave-active{
      transition: all 0.5s
    }
    &.fade-enter, &.fade-leave-active{
      opacity: 0
    }  
    .header {
      display: flex;
      justify-content: space-between;
      background-color: #f3f5f7;
      padding: 10px 20px;
      border-bottom: 1px solid rgba(7, 17, 27, 0.1);

      .header-text {
        color: #07111b;
        font-size: 14px;
      }

      .close {
        color: #00a0dc;
        font-size: 12px;
      }
    }

    .wrapper {
      max-height: 200px;
      overflow: hidden
      .content{
        .listWrapper {
          border-bottom: solid 1px rgba(7, 17, 27, 0.1);
          display: flex;
          justify-content: space-between;
          height: 26px;
          line-height: 26px;
          padding: 10px 0;
          margin: 0 15px;
          .list-left {
            width: 70%;
            display: flex;
            justify-content: space-between;

            .price {
              color: red;
              font-weight: 400;
            }
          }

          .addGoods {
            // vertical-align middle
          }
        }
      }
    }
  }

  .accounts {
    z-index: 200;
    background-color: #00b43c;
    position: fixed;
    right: 0;
    height: 48px;
    width: 100px;
    color: white;
    line-height: 48px;
    text-align: center;
    font-weight: 700;

    &.delivery-price {
      font-size: 10px;
      width: 110px;
      font-weight: 200;
      color: rgba(255, 255, 255, 0.4);
      display: inline-block;
      background-color: rgba(255, 255, 255, 0.1);
    }
  }

  .card-wrapper {
    position: relative;
    z-index: 200;

    .card-icon {
      position: absolute;
      text-align: center;
      margin: 0 0 19px 18px;
      border-radius: 50%;
      width: 44px;
      height: 44px;
      background-color: #2b343c;
      box-shadow: 0 0 0 8px #141d27;
      display: inline-block;

      &.showtotalGoods {
        background-color: #00a0dc;

        .icon-play {
          color: white;
        }
      }

      .icon-play {
        text-align: center;
        line-height: 48px;
        font-size: 24px;
        height: 48px;
        color: rgba(255, 255, 255, 0.4);
      }
    }

    .goodsNum {
      position: absolute;
      left: 42px;
      bottom: -10px;
      color: white;
      border: solid red 1px;
      background-color: red;
      padding: 2px 8px;
      font-weight: 500;
      border-radius: 50%;
      font-size: 10px;
    }

    .total-price {
      position: absolute;
      vertical-align: top;
      left: 80px;
      height: 24px;
      line-height: 24px;
      font-size: 16px;
      color: rgba(255, 255, 255, 0.4);
      font-weight: 700;
      margin: 12px 0px;
      padding-right: 12px;
      border-right: 1px solid rgba(255, 255, 255, 0.1);

      &.showTotalPrice {
        color: white;
      }
    }
  }

  .ball-container {
    .ball {
      position: fixed;
      left: 32px;
      bottom: 15px;
      z-index: 200;
      transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);

      .inner {
        width: 16px;
        height: 15px;
        border-radius: 50%;
        background: rgb(0, 160, 220);
        transition: all 0.4s linear;
      }
    }
  }
}
</style>
