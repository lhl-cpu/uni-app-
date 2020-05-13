<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommend_view"
    scroll-y
    v-if="recommends.length"
  >
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <navigator
        class="recommend_item"
        v-for="(item, index) in recommends"
        :key="index"
        :url="`/pages/ablum/index?id=${item.target}`"
      >
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐结束 -->

    <!-- 月份开始 -->
    <view class="moneths_wrap">
      <view class="moneths_title">
        <view class="moneths_title_info">
          <view class="moneths_info">
            <text>{{ monthes.DD }}</text>
            /{{ monthes.MM }}月
          </view>
          <view class="moneths_text">{{ monthes.title }}</view>
        </view>
        <view class="moneths_title_more">更多 ></view>
      </view>
      <view class="moneths_content">
        <view
          class="moneths_item"
          v-for="(item, index) in monthes.items"
          :key="index"
        >
          <godetail :list="monthes.items" :index="index">
            <image
              mode="aspectFill"
              :src="item.thumb + item.rule.replace('$<Height>', 360)"
            ></image>
          </godetail>
        </view>
      </view>
    </view>
    <!-- 月份结束 -->

    <!-- 热门开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hot_item" v-for="(item, index) in hots" :key="index">
          <godetail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb"></image>
          </godetail>
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
import godetail from "../../../components/goDetail";
export default {
  data() {
    return {
      recommends: [],
      monthes: {},
      hots: [],
      params: {
        limit: 30,
        order: "hot",
        skip: 0,
      },
      havemore: true,
    };
  },
  components: {
    godetail,
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: "推荐",
    });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params,
      }).then((result) => {
        if (result.res.vertical.length === 0) {
          this.havemore = false;
          return;
        }
        if (this.recommends.length === 0) {
          this.recommends = result.res.homepage[1].items;
          this.monthes = result.res.homepage[2];
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        this.hots = [...this.hots, ...result.res.vertical];
      });
    },
    handleToLower: function() {
      if (this.havemore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showTabBar({
          title: "到底了",
          icno: "none",
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.recommend_view {
  height: calc(100vh - 36px);
}

.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}

.moneths_wrap {
  .moneths_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .moneths_title_info {
      display: flex;
      .moneths_info {
        color: $themeColor;
        font-weight: 600;
        text {
          font-size: 36rpx;
        }
      }

      .moneths_text {
        padding-left: 15rpx;
        font-weight: 600;
        font-size: 34rpx;
        color: #666;
      }
    }

    .moneths_title_more {
      font-size: 24rpx;
      color: $themeColor;
      font-weight: 600;
    }
  }

  .moneths_content {
    display: flex;
    flex-wrap: wrap;
    .moneths_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}

.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      padding-left: 20rpx;
      border-left: 10rpx solid $themeColor;
      font-size: 30rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
</style>
