<template>
  <view class="video_play">
    <image :src="videoObj.img"></image>
    <!-- 工具栏开始 -->
    <view class="video_tool">
      <view
        :class="['iconfont', muted ? 'iconjingyin' : 'iconshengyin']"
        @click="changmuted"
      ></view>
      <view class="iconfont iconzhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <!-- 工具栏结束 -->

    <!-- 视频开始 -->
    <view class="video_wrap">
      <video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
    </view>
    <!-- 视频结束 -->

    <!-- 下载按钮 -->
    <view class="download">
      <view class="download_btn" @click="handledownload">
        下载高清
      </view>
    </view>
    <!-- 下载按钮结束 -->
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoObj: {},
      muted: false,
    };
  },
  onLoad() {
    this.videoObj = getApp().globalData.video;
  },
  methods: {
    changmuted() {
      this.muted = !this.muted;
    },
    async handledownload() {
      await uni.showLoading({ title: "下载中" });

      const { temFilePath } = (
        await uni.downloadFile({ url: this.videoObj.video })
      )[1];
      await uni.saveVideoToPhotosAlbum({
        filePath: temFilePath,
      });

      uni.hideLoading();
      await uni.showToast({
        title: "下载成功",
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;
  image {
    position: absolute;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    //css3滤镜
    filter: blur(15px);
  }
  .video_tool {
    display: flex;
    height: 80rpx;
    justify-content: flex-end;
    padding: 20rpx 0;
    .iconfont {
      width: 80px;
      color: #fff;
      font-size: 50rpx;
      border-radius: 40rpx;
      background-color: rgba(0, 0, 0, 0.2);
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 20rpx;
    }
    .iconzhuanfa {
      position: relative;
      button {
        position: absolute;
        width: 100%;
        height: 100%;
        opacity: 0;
      }
    }
  }

  .video_wrap {
    display: flex;
    justify-content: center;
    video {
      width: 360rpx;
      height: 600rpx;
    }
  }

  .download {
    padding-top: 20rpx;
    display: flex;
    justify-content: center;
    .download_btn {
      width: 360rpx;
      height: 80rpx;
      border-radius: 40rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
      border: 3rpx solid #fff;
      background-color: rgba(0, 0, 0, 0.6);
    }
  }
}
</style>
