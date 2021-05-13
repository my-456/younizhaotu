<template>
  <!-- 轮播图 -->
  <scroll-view :v-if="!this.banner.length===0" scroll-y class="scroll" @scrolltolower="handleTolower">
    <view class="album_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb"></image>
        </swiper-item>
      </swiper>
    </view>

    <!-- 列表 -->
    <view class="album_list">
      <navigator  v-for="item in album" :url="`/pages/album/album?id=${item.id}`" class="album_item" :key="item.id">
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover"></image>
        </view>
        <view class="album_info">
          <view class="album_name">{{ item.name }}</view>
          <view class="album_desc">{{ item.desc }}</view>
          <view class="album_btm">
            <view class="album_attention">关注</view>
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
      data: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      banner: [],
      album: [],
      hisNext:true,
      end:true
    };
  },

  mounted() {
    uni.setNavigationBarTitle({ title: "专辑" });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.data,
      }).then((res) => {
        console.log(res);
        if(this.banner.length===0){
        this.banner = res.res.banner;
        }
        if(res.res.album.length===0){
          this.hisNext=false
          
        }
        this.album =[...this.album,...res.res.album];
      });
    },
    handleTolower() {
      if(this.hisNext){
      this.data.skip+=this.data.limit;
      this.getList();
      }else{
        console.log("触底了");
        uni.showToast({
          title:"没有更多数据了",
          icon:"none"
        })
      }
      
    },
  },
};
</script>

<style lang="scss" scoped>
.scroll{
  height: 100vh ;
  .end{
    background-color: $color;
    text-align: center;
    font-size: 30rpx;
    padding: 10rpx 0;
  }
}
.album_swiper {
  swiper {
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}
// 列表部分
.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    border-bottom: 1rpx solid #ccc;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      // display: flex;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        font-weight: 600;
        color: #000;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;

        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btm {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          border: 2px solid $color;
          color: $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>