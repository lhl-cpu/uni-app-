<template>
  <view class="home_category">
    <navigator class="cate_item" v-for="(item, index) in category" :key="index" :url="`/pages/imgCatefory/index?id=${item.id}`">
      <image :src="item.cover" mode="aspectFill"></image>
      <view class="category_name">
        {{ item.name }}
      </view>
    </navigator>
  </view>
</template>

<script>
export default {
  data() {
    return {
      category: [],
    };
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: "分类",
    });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/vertical/category",
      }).then((result) => {
        result.res.category.forEach((v) => {
          this.category = result.res.category;
        });
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.home_category {
  display: flex;
  flex-wrap: wrap;
  .cate_item {
    position: relative;
    width: 33.33%;
    border: 5rpx solid #fff;
    image {
      height: 240rpx;
    }
    .category_name {
      position: absolute;
      height: 50rpx;
      width: 100%;
      left: 0;
      bottom: 0;
      color: #fff;
      background-image: linear-gradient(
        to right top,
        rgba(0, 0, 0, 0.2),
        rgba(0, 0, 0, 0)
      );
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}
</style>
