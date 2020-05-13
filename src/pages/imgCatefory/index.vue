<template>
  <view>
    <view class="category_tab">
      <view class="category_tab_title">
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
    </view>
    <scroll-view
      @scrolltolower="handleScrolltolower"
      scroll-y
      enable-flex
      class="category_tab_content"
    >
      <view class="list" v-for="(item, index) in vertical" :key="index">
        <godetial :list="vertical" :index="index">
          <image :src="item.thumb" mode="widthFix"></image>
        </godetial>
      </view>
    </scroll-view>
  </view>
</template>

<script>
import godetial from "../../components/goDetail";
import uniSegmentedControl from "../../../node_modules/@dcloudio/uni-ui/lib/uni-segmented-control/uni-segmented-control.vue";
export default {
  components: {
    uniSegmentedControl,
    godetial,
  },
  data() {
    return {
      items: [
        { title: "最新", order: "new" },
        { title: "热门", order: "hot" },
      ],
      params: {
        limit: 30,
        skip: 0,
        order: "new",
      },
      current: 0,
      id: 0,
      vertical: [],
      havemore: true,
    };
  },
  onLoad(e) {
    this.id = e.id;
    this.getList();
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      } else {
        //重复点击同一标题
        return;
      }
      this.params.order = this.items[e.currentIndex].order;
      this.params.skip = 0;
      this.vertical = [];
      this.getList();
    },
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params,
      }).then((result) => {
        if (result.res.vertical.length === 0) {
          this.havemore = false;
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          return;
        }
        this.vertical = [...this.vertical, ...result.res.vertical];
      });
    },
    handleScrolltolower() {
      if (this.havemore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有更多数据了",
          icon: "none",
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.category_tab_title {
  position: relative;
  .title_inner {
    width: 60%;
    margin: 0 auto;
  }
  .iconsearch {
    position: absolute;
    top: 45%;
    transform: translateY(-50%);
    right: 6%;
  }
}

.category_tab_content {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .list {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
  image {
  }
}
</style>
