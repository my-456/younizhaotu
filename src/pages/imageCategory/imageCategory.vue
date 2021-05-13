<template>
  <view>
    <view class="category_tab">
      <view class="category_tab_title">
        <!-- 标题 -->
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map((v) => v.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d4237a"
          ></uni-segmented-control>
        </view>
        <!-- 图标 -->
        <view class="iconfont"
          ><text class="iconfont .icon-sousuo"></text
        ></view>
      </view>
      <!-- 内容 -->
      <scroll-view
        enable-flex
        scroll-y
        @scrolltolower="handleTolower"
        class="category_tab_content"
      >
        <view class="cate_item" v-for="item in vertical" :key="item.id">
          <image mode="widthFix" :src="item.thumb"></image>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
//引入分段器
import { uniSegmentedControl } from "@dcloudio/uni-ui";
export default {
  components: {
    uniSegmentedControl,
  },
  data() {
    return {
      items: [
        {
          title: "最新",
          order: "new",
        },
        {
          title: "热门",
          order: "hot",
        },
      ],
      current: 0,
      params: {
        limit:30,
        skip: 0,
        order: "new",
      },
      id: 0,
      vertical: [],
      hasNext: true,
    };
  },
  onLoad(options) {
    this.id = options.id;
    this.getList();
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }else{
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
      }).then((res) => {
        console.log(res);
        if (res.res.vertical.length === 0) {
          this.hasNext = false;
          return;
        }
        this.vertical = [...this.vertical, ...res.res.vertical];
      });
    },
    handleTolower() {
      if (this.hasNext) {
        this.params.skip += this.params.limit;
        this.getList();
      }else{
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
.category_tab {
  .category_tab_title {
    position: relative;
    .title_inner {
      width: 60%;
      margin: 0 auto;
    }

    .iconfont {
      .icon-sousuo {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        right: 5%;
      }
    }
  }

  .category_tab_content {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .cate_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
</style>