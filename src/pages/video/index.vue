<template>
  <view class="video_tab">
    <view class="video_tab_title">
      <view class="title_inner">
        <uni-segmented-control
          :current="current"
          :values="items.map((v) => v.title)"
          @clickItem="onClickItem"
          style-type="text"
          active-color="#e63a7d"
        ></uni-segmented-control>
      </view>
    </view>
    <view class="video_tab_content">
      <view v-if="current < 4"> <videomain :urlobj="{url:items[current].url,params:items[current].params}"></videomain></view>
      <view v-else> <videocategory></videocategory></view>
    </view>
  </view>
</template>

<script>
import uniSegmentedControl from "../../../node_modules/@dcloudio/uni-ui/lib/uni-segmented-control/uni-segmented-control.vue";
import videomain from "./video-main/index";
import videocategory from "./video-category/index";
export default {
  components: {
    uniSegmentedControl,
    videomain,
    videocategory,
  },
  data() {
    return {
      items: [
        {
          title: "推荐",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/featured",
          params: { limit: 30, skip: 0, order: "hot" },
        },
        {
          title: "娱乐",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/category/59b25abbe7bce76bc834198a",
          params: { limit: 30, skip: 0, order: "new" },
        },
        {
          title: "最新",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/videowp",
          params: { limit: 30, skip: 0, order: "new" },
        },
        {
          title: "热门",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/videowp",
          params: { limit: 30, skip: 0, order: "hot" },
        },
        {
          title: "分类",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/category",
          params: {},
        },
      ],
      current: 0,
    };
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.video_tab_title {
  position: relative;
  .title_inner {
    width: 80%;
    margin: 0 auto;
  }
}
</style>
