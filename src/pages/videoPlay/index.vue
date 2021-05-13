<template>
  <view class="video_play">
    <image :src="videoObj.img"></image>

    <!-- 工具栏 -->
    <view class="video_tool">
      <view
        @click="this.muted = !this.muted"
        :class="['iconfont', muted ? 'icon-jingyin' : 'icon-shengyin']"
      ></view>
      <view class="iconfont icon-hanhan-01-01">
        <!-- 转发 -->
        <button open-type="share"></button>
      </view>
    </view>
    <!-- 视频开始 objectFit拉伸-->
    <view class="video_wrap">
      <video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
    </view>

    <!-- 下载 -->
    <view class="download">
      <view @click="handleDownload" class="download_btn">下载高清</view>
    </view>
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
    console.log(getApp().globalData);
    this.videoObj = getApp().globalData.video;
  },
  methods:{
     async handleDownload(){
         await uni.showLoading({
             title:"下载中"
         })
          //将远程文件下载到小程序内存
         const {tempFilePath}=(await uni.downloadFile({
                url:this.videoObj.video
            }))[1]

          //将内存中的文件下载到本地
         await uni.saveVideoToPhotosAlbum({
              filePath:tempFilePath
          })

          uni.hideLoading();

          await uni.showToast({
              title:"下载成功"
          })
      }
  }
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
    //css3 滤镜
    filter: blur(20px);
  }

  .video_tool {
    height: 80rpx;
    display: flex;
    justify-content: flex-end;

    .iconfont {
      display: flex;
      width: 88rpx;
      color: #fff;
      font-size: 50rpx;
      border-radius: 40rpx;
      background-color: rgba(0, 0, 0, 0.2);
      justify-content: center;
      align-items: center;
      margin-right: 20rpx;
    }

    .icon-hanhan-01-01 {
        position:relative;
        button{
            position: absolute;
            width: 100%;
            height: 100%;
            //透明度
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
    display: flex;
    justify-content: center;
    margin-top: 30rpx;

    .download_btn {
      width: 360rpx;
      height: 80rpx;
      border-radius: 40rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
      border: 1rpx solid #fff;
      background-color: rgba(0, 0, 0, 0.6);
    }
  }
}
</style>