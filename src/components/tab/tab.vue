<template>
  <div class="tab border-1px">
    <router-link to='/seller'>商家</router-link>
    <router-link to='/goods'>商品</router-link>
    <router-link to='/ratings' >评论</router-link>
  </div>
</template>

<script>
import "../../common/stylus/index.styl";
export default {
  props : {
    seller : {
      type : Object
    }
  }
}
</script>
<style lang="stylus" scoped>
@import '../../common/stylus/index.styl';
.tab 
  display: flex;
  height: 50px;
  line-height: 50px;
  border-1px(rgba(7, 17, 27, 0.1));
  a 
    flex-grow: 1;
    text-align: center;
    &.router-link-active 
      color: red;
</style>
