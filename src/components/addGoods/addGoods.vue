<template>
  <div class="addGoods" :class="{'active' : food.count}">
    <span @click.stop.prevent="decreaseCard" class="icon-minus-alt decrease"></span>
    <span class="text">{{food.count}}</span>
    <span class="icon-plus-alt add" @click.stop.prevent="addCard"></span>
  </div>
</template>
<script>
import Vue from 'vue'
  export default {
    props : {
      food : {
        type : Object
      }
    },
    methods : {
      decreaseCard(event){
        this.food.count > 0 ? this.food.count-- : 0
      },
      addCard(event){
        if(!this.food.count){
          Vue.set(this.food,"count",1)
        }else{
          this.food.count++
        }
        this.$emit('getSelected', event.target)
      }
    },
  }
</script>
<style lang="stylus" scoped>
@import '../../common/stylus/icon.css'

.addGoods
  width 72px
  position relative
  text-align center
  font-size 16px
  span 
    line-height 24px
    width 24px
    height 24px
    display inline-block
    color green
    position absolute
    transition all 1.0s
    left 48px
    &.decrease
      opacity 0
      transform rotate(180deg)
    &.text
      opacity 0
  &.active
    .add
      left 48px
    .decrease
      left 0
      opacity 1
      transform rotate(0deg)
    .text
      left 24px
      opacity 1
</style>
