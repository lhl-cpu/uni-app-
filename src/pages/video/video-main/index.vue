<template>
  <scroll-view
    scroll-y
    enable-flex
    class="video_main"
    @scrolltolower="handleScrolltolower"
  >
    <view
      class="video_item"
      v-for="(item, index) in videolist"
      :key="index"
      @click="handleGoVideo(item)"
    >
      <image mode="widthFix" :src="item.img"></image>
    </view>
  </scroll-view>
</template>

<script>
export default {
  props: {
    urlobj: Object,
  },
  data() {
    return {
      videolist: [],
      havemore: true,
    };
  },
  watch: {
    urlobj() {
      this.videolist = [];
      this.getList();
    },
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params,
      }).then((result) => {
        console.log(result);
        if (result.res.videowp.length === 0) {
          this.havemore = false;
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          return;
        }
        this.videolist = [...this.videolist, ...result.res.videowp];
      });
    },
    handleScrolltolower() {
      console.log("1231");
      if (this.havemore) {
        this.urlobj.params.skip += this.urlobj.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有更多数据了",
          icon: "none",
        });
      }
    },
    handleGoVideo(item) {
      getApp().globalData.video = item;
      uni.navigateTo({
          url:"/pages/videoPlay/index"
      })
    },
  },
};
</script>

<style lang="scss" scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
}
</style>
