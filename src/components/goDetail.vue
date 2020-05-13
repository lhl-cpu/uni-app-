<template>
  <view @click="handedetail">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: ["list", "index"],
  methods: {
    handedetail() {
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;

      uni.navigateTo({
        url: "/pages/imgDetail/index",
      });
    },
  },
};
</script>

<style lang="scss" scoped></style>
