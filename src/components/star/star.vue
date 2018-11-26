<template>
  <div class="stars" :class="starType">
    <span v-for="(item,index) in initstarsMap" :key="index" class="star-item" :class="starsMap[index]" alt="" />
  </div>
</template>
<style lang="stylus" scoped>
@import '../../common/stylus/index.styl';

.stars 
  font-size 0
  .star-item
    display inline-block
    background-repeat no-repeat
  &.star-24
    .star-item
      width: 10px;
      height: 10px;
      background-size: 10px 10px;
      margin-right 3px
      &.last-child
        margin-right 0
    .on 
      bg-image('star24_on');
    .off 
      bg-image('star24_off');
    .half 
      bg-image('star24_half');
  &.star-36
    .star-item
      width: 15px;
      height: 15px;
      background-size: 15px 15px;
      margin-right 6px
      &.last-child
        margin-right 0
    .on 
      bg-image('star36_on');
    .off 
      bg-image('star36_off');
    .half 
      bg-image('star36_half');
  &.star-48
    .star-item
      width: 20px;
      height: 20px;
      background-size: 20px 20px;
      margin-right 8px
      &.last-child
        margin-right 0
    .on 
      bg-image('star48_on');
    .off 
      bg-image('star48_off');
    .half 
      bg-image('star48_half');
</style>
<script>
export default {
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  data() {
    return {
      starsMap: ["off", "off", "off", "off", "off"]
    };
  },
  computed: {
    starType(){
       return 'star-' + this.size
    },
    initstarsMap() {
      let starsMap = this.starsMap;
      let score = this.score
      if(score == 0.5){
        starsMap[0] ="half";
        return starsMap
      }
      let onNum = Math.round(score)
      let pointNum = (score - onNum + '').split('.')[1]
      for (var i = 0; i < onNum; i++) {
        starsMap[i] = "on";
      }
      if(pointNum == 5){
        starsMap[onNum -1 ] = "half";
      }else if(pointNum > 5){
        starsMap[onNum -1 ] ="on";
      }
      return starsMap;
    }
  },
  methods: {}
};
</script>