<template>
  <view>
    <!-- 专辑背景图开始 -->
    <view class="album_bg">
      <image mode="widthFix" :src="album.cover"></image>
      <view class="album_info">
        <view class="ablum_name">{{ album.name }}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>
    <!-- 专辑背景图结束 -->

    <!-- 专辑作者开始 -->
    <view class="album_author">
      <view class="ablum_author_info">
        <image mode="widthFix" :src="album.user.avatar"></image>
        <view class="author_name">{{ album.user.name }}</view>
      </view>
      <view class="ablu_author_desc">
        <text>{{ album.desc }}</text>
      </view>
    </view>
    <!-- 专辑作者结束 -->

    <view class="ablum_list">
      <view class="ablum_item" v-for="(item, index) in wallpaper" :key="index">
        <godetail :list="wallpaper" :index="index">
          <image
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
          ></image>
        </godetail>
      </view>
    </view>
  </view>
</template>

<script>
import godetail from "../../components/goDetail";
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
        first: 1,
      },
      id: -1,
      album: {},
      wallpaper: [],
      havemore: true,
    };
  },
  components: {
    godetail,
  },
  onLoad(options) {
    this.id = options.id;
    this.getList();
  },
  onReachBottom() {
    if (this.havemore) {
      this.params.skip += this.params.limit;
      this.getList();
    } else {
      uni.showToast({
        title: "到底了",
        icon: "none",
      });
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params,
      }).then((result) => {
        if (Object.keys(this.album).length === 0) {
          this.album = result.res.album;
        }
        if (result.res.wallpaper.length === 0) {
          this.havemore = false;
        }
        this.wallpaper = [...this.wallpaper, ...result.res.wallpaper];
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.album_bg {
  position: relative;

  .album_info {
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 80rpx;
    color: #fff;
    padding: 0 15rpx;
    .ablum_name {
      font-size: 40rpx;
    }

    .album_btn {
      background-color: $themeColor;
      width: 152rpx;
      height: 60rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}

.album_author {
  padding: 10rpx;
  .ablum_author_info {
    display: flex;
    padding: 10rpx 0;
    image {
      border-radius: 50%;
      width: 50rpx;
    }

    .author_name {
      color: #000;
      margin-left: 15rpx;
    }
  }
}

.ablum_list {
  display: flex;
  flex-wrap: wrap;
  .ablum_item {
    width: 33.33%;
    border: 3rpx solid #fff;
    image {
      height: 160rpx;
    }
  }
}
</style>
