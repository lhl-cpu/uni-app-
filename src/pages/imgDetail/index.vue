<template>
  <view>
    <!-- 用户信息开始 -->
    <view class="user_info">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix"></image>
      </view>
      <view class="user_desc">
        <view class="user_name">{{ imgDetail.user.name }}</view>
        <view class="user_time">{{ imgDetail.ctime }}</view>
      </view>
    </view>
    <!-- 用户信息结束 -->

    <!-- 高清大图开始 -->
    <view class="high_img">
      <swipter @swipteraction="handleswipterAction">
        <image mode="widthFix" :src="imgDetail.thumb"></image>
      </swipter>
    </view>
    <!-- 高清大图结束 -->

    <!-- 点赞开始 -->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icondianzan">{{ imgDetail.rank }}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont iconshoucang">收藏</text>
      </view>
    </view>
    <!-- 点赞结束 -->

    <!-- 专辑开始 -->
    <view class="album_wrap" v-if="album.length">
      <view class="album_title">相关</view>
      <view class="album_list">
        <view class="album_item" v-for="(item, index) in album" :key="index">
          <view class="item_img">
            <image mode="aspectFill" :src="item.cover"></image>
          </view>
          <view class="item-info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{ item.name }}</view>
            <text class="iconfont iconiconfontjiantou4"></text>
          </view>
        </view>
      </view>
    </view>
    <!-- 专辑结束 -->

    <!-- 最热评论 -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont iconhot1"></text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="(item, index) in hot" :key="index">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar"></image>
            </view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cntime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="(item2, index) in item.user.title"
                :key="index"
                :src="item2.icon"
              ></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan"></text>
              <view class="like_size">{{ item.size }}</view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最热结束 -->

    <!-- 最新评论 -->
    <view class="comment new" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont iconpinglun"></text>
        <text class="comment_text">最新评论</text>
      </view>
      <view class="comment_list">
        <view
          class="comment_item"
          v-for="(item, index) in comment"
          :key="index"
        >
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar"></image>
            </view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cntime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="(item2, index) in item.user.title"
                :key="index"
                :src="item2.icon"
              ></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan"></text>
              <view class="like_size">{{ item.size }}</view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新结束 -->

    <!-- 下载开始 -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
    <!-- 下载结束 -->
  </view>
</template>

<script>
import moment from "moment";
moment.locale("zh-cn");
import swipter from "../../components/swiperAction";
export default {
  data() {
    return {
      imgDetail: {},
      album: [],
      //最热评论
      hot: [],
      //最新评论
      comment: [],
      imgIndex: 0,
    };
  },
  components: {
    swipter,
  },
  onLoad() {
    const { imgIndex } = getApp().globalData;
    this.imgIndex = imgIndex;

    this.getData();
  },
  methods: {
    getData() {
      const { imgList } = getApp().globalData;

      this.imgDetail = imgList[this.imgIndex];

      this.imgDetail.ctime = moment(this.imgDetail.atime * 1000).fromNow();

      this.getComments(this.imgDetail.id);
    },
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`,
      }).then((result) => {
        this.album = result.res.album;

        result.res.hot.forEach(
          (v) => (v.cntime = moment(v.atime * 1000).fromNow())
        );
        result.res.comment.forEach(
          (v) => (v.cntime = moment(v.atime * 1000).fromNow())
        );

        this.hot = result.res.hot;
        this.comment = result.res.comment;
      });
    },
    handleswipterAction(event) {
      const { imgList } = getApp().globalData;
      if (event.direction === "right" && this.imgIndex > 0) {
        this.imgIndex -= 1;
        this.getData();
      } else if (
        event.direction === "left" &&
        this.imgIndex < imgList.length - 1
      ) {
        this.imgIndex += 1;
        this.getData();
      } else {
        uni.showToast({
          title: "没有更多数据了",
          icon: "none",
        });
      }
    },
    async handleDownload() {
      await uni.showLoading({
        title: "下载中",
      });
      //远程文件下载到内存中
      const result1 = await uni.downloadFile({ url: this.imgDetail.img });
      const tempFilePath = result1[1].tempFilePath;
      //内存中的临时文件下载到本地
      const resutl2 = await uni.saveImageToPhotosAlbum({
        filePath: tempFilePath,
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
.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: #000;
      font-weight: 666;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 2rpx solid #ccc;
  .rank {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
  }

  .user_collect {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
  }
}

.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
    color: #000;
    font-size: 30rpx;
    font-weight: 600;
  }

  .album_list {
    .album_item {
      border-bottom: 2rpx solid #ccc;
      display: flex;
      padding: 10rpx 0;
      .item_img {
        flex: 1;
        image {
          height: 180rpx;
          width: 180rpx;
        }
      }

      .item-info {
        flex: 3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          border-radius: 10%;
          width: 100rpx;
          height: 50rpx;
          background-color: $themeColor;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
        .iconfont {
          position: absolute;
          font-size: 40rpx;
          right: 10%;
          bottom: 50%;
          color: #666;
        }
      }
    }
  }
}

.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    text.comment_text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      .comment_user {
        display: flex;
        padding: 20rpx 0;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          image {
            width: 90%;
          }
        }
        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }
    }

    .comment_desc {
      display: flex;
      padding: 20rpx 0;
      .comment_content {
        flex: 6;
        padding-left: 15%;
        color: #000;
      }

      .comment_like {
        flex: 1;
        text-align: right;
        display: flex;
        .iconfont {
        }

        .like_size {
        }
      }
    }
  }
}
.new {
  .comment_title {
    .iconpinglun {
      color: aqua;
    }
  }
}

.download {
  height: 100rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn {
    width: 90%;
    height: 80%;
    background-color: $themeColor;
    color: #fff;
    font-size: 40rpx;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
