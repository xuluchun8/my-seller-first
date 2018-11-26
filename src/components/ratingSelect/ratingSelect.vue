<template>
  <div class="selectButton">
    <ul class="selectList" v-if="ratingSelectInfo">
      <li @click="selectedAll" :class="{'active' : selectedInfo == 'all'}">
        <span >全部{{ratingSelectInfo.all}}</span>
      </li>
      <li @click="selectedRecommend" :class="{'active' : selectedInfo == 'recommend'}">
        <span>推荐{{ratingSelectInfo.recommendNum}}</span>
      </li>
      <li @click="selectedNotRecommend" :class="{'active' : selectedInfo == 'notRecommend'}">
        <span>吐槽{{ratingSelectInfo.notRecommendNum}}</span>
      </li>
    </ul>
  </div>
</template>
<script>
  export default {
    props : {
      ratingSelectInfo : {
        type : Object
      }
    },
    methods : {
      selectedAll(){
        this.selectedInfo = 'all'
        this.$emit('getSelectInfo',this.selectedInfo)
      },
      selectedRecommend(){
        this.selectedInfo = 'recommend'
        this.$emit('getSelectInfo',this.selectedInfo)
      },
      selectedNotRecommend(){
        this.selectedInfo = 'notRecommend'
        this.$emit('getSelectInfo',this.selectedInfo)
      }
    },
    data(){
      return { 
        selectedInfo : 'all'
      }
    }
  }
</script>
<style lang="stylus" scoped>
  .selectList
    display flex
    li 
      padding 10px
      background-color rgba(0,160,220,0.2)
      margin-right 10px
      color gray
      &.active
        background-color #00a0dc
        color white
</style>
