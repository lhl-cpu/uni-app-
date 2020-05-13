<template>
  <view class="home_tab">
    <view class="home_tab_title">
      <view class="title_inner">
        <uni-segmented-control
          :current="current"
          :values="items.map((v) => v.title)"
          @clickItem="onClickItem"
          style-type="text"
          active-color="#e63a7d"
        ></uni-segmented-control>
      </view>
      <view class="iconfont iconsearch"></view>
    </view>
    <view class="home_tab_content">
      <view v-if="current === 0">
        <recommend></recommend>
      </view>
      <view v-if="current === 1">
        <category></category>
      </view>
      <view v-if="current === 2">
        <anew></anew>
      </view>
      <view v-if="current === 3">
        <album></album>
      </view>
    </view>
  </view>
</template>

<script>
import recommend from "./home-recommend/index.vue";
import category from "./home-category/index.vue";
import anew from "./home-new/index.vue";
import album from "./home-album/index.vue";
import uniSegmentedControl from "../../../node_modules/@dcloudio/uni-ui/lib/uni-segmented-control/uni-segmented-control.vue";

export default {
  components: {
    recommend,
    category,
    anew,
    album,
    uniSegmentedControl,
  },
  data() {
    return {
      items: [
        { title: "推荐" },
        { title: "分类" },
        { title: "最新" },
        { title: "专辑" },
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
  }
};
</script>

<style lang="scss">
.home_tab_title {
  position: relative;
  .title_inner {
    width: 70%;
    margin: 0 auto;
  }
  .iconsearch {
    position: absolute;
    top: 45%;
    transform: translateY(-50%);
    right: 6%;
  }
}
.home_tab_content {
}
</style>
