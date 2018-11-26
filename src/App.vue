<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <v-tab></v-tab>
    <keep-alive>
      <router-view class="hide" :seller = "seller"></router-view>
    </keep-alive>

  </div>
</template>

<script>
import Header from "components/header/header";
import Tab from "components/tab/tab";
export default {
  name: "App",
  data() {
    return {
      seller: {}
    };
  },
  components: {
    "v-header": Header,
    "v-tab": Tab
  },
  methods: {
    show(){
    }
  },
  created() {
    this.$http.get("/api/seller").then(res => {
      this.seller = res.data.data
    });
  }
};
</script>

<style>
</style>
