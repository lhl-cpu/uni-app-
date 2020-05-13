<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="album_view"
    scroll-y
    v-if="album.length"
  >
    <view class="album_swiper">
      <swiper autoplay indicator circular>
        <swiper-item v-for="(item, index) in banner" :key="index">
          <image mode="widthFix" :src="item.thumb"></image>
        </swiper-item>
      </swiper>
    </view>

    <view class="album_list">
      <navigator
        class="album_item"
        v-for="(item, index) in album"
        :key="index"
        :url="`/pages/ablum/index?id=${item.id}`"
      >
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover"></image>
        </view>
        <view class="album_info">
          <view class="album_name">{{ item.name }}</view>
          <view class="album_desc">{{ item.desc }}</view>
          <view class="album_btn">
            <view class="album_attention">+ 关注</view>
          </view>
        </view>
      </navigator>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      banner: [],
      album: [],
      havemore: true,
    };
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: "专辑",
    });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params,
      }).then((result) => {
        if (result.res.album.length === 0) {
          this.havemore = false;
          return;
        }

        if (this.banner.length === 0) {
          this.banner = result.res.banner;
        }

        this.album = [...this.album, ...result.res.album];
      });
    },
    handleToLower() {
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
  },
};
</script>

<style lang="scss" scoped>
.album_view {
  height: calc(100vh - 36px);
}

.album_swiper {
  swiper {
    height: 326.1rpx;
    image {
      height: 100%;
    }
  }
}

.album_list {
  padding: 10rpx;
  .album_item {
    display: flex;
    padding: 10rpx 0;
    border-bottom: 2rpx solid #ccc;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        widows: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;

        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        display: flex;
        justify-content: flex-end;
        padding: 10rpx;

        .album_attention {
          color: $themeColor;
          border: 2rpx solid $themeColor;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>
