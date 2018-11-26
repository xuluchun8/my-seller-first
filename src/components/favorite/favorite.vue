<template>
  <div class="favorite">
    <span class="heart" :class="{'icon-heart-stroke':!favorite, 'icon-heart-fill':favorite}"></span>
    <span class="text">{{favorite == false ? text : ("已" + text)}}</span>
  </div>
</template>
<script>

  export default {
    props : ['text','favorite']
  }
</script>
<style lang="stylus" scoped>
  @import '../../common/stylus/icon.css';
  .favorite
    text-align center
    width 40px
    line-height 20px
    .heart
      display block
      font-size 20px
      color red 
    .text 
      color gray 
      font-size 12px
</style>
