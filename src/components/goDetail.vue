<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: Array,
    index: Number,
  },
  mounted() {
   // console.log(111,this.list);
  },
  methods: {
    handleClick() {
      //将数据缓存
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;
      //实现点击跳转事件
      uni.navigateTo({
        url: "/pages/imgDetail/imgDetail",
      });
    },
  },
};
</script>

<style>
</style>