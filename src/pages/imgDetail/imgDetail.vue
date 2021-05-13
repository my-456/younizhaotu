<template>
  <!-- 图片详情 -->
  <view>
    <!-- 用户信息 开始 -->
    <view class="user_info" v-if="imgDetail.user">
      <view class="user_icon"
        ><image mode="widthFix" :src="imgDetail.user.avatar"></image
      ></view>
      <view class="user_desc">
        <view class="user_name">{{ imgDetail.user.name }}</view>
        <view class="user_time">{{ imgDetail.cnTime }}</view>
      </view>
    </view>
    <!-- 用户信息结束 -->
    <!-- 高清大图开始 -->
    <swiper-action @swiperAction="handleSwiperAction">
      <view class="high_img">
        <image mode="widthFix" :src="imgDetail.thumb"></image>
      </view>
    </swiper-action>
    <!-- 高清大图结束 -->

    <!-- 点赞 开始 -->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icon-dianzan">{{ imgDetail.rank }}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont icon-shoucang">收藏</text>
      </view>
    </view>
    <!-- 点赞结束 -->
    <!-- 专辑 开始 -->
    <view class="album_wrap" v-if="album.legth">
      <!-- 标题 -->
      <view class="album_title">相关</view>
      <!-- 内容 -->
      <view class="album_list">
        <view class="album_item" v-for="item in album" :key="item.id">
          <view class="album_cover">
            <image :src="item.cover" mode="aspectFill"></image>
          </view>
          <!-- 右边 -->
          <view class="album_info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{ item.name }}</view>
            <text class="iconfont icon-youjiantou"></text>
          </view>
        </view>
      </view>
    </view>
    <!-- 专辑 结束 -->
    <!-- 最热评论 -->
    <view class="comment hot" v-if="hot.length">
        <view class="comment_title">
            <text class="iconfont icon-hot"></text>
            <text class="comment_text">最热评论</text>
        </view>
        <view class="comment_list">
            <view class="commet_item"
            v-for="item in hot"
            :key="item.id"
            >
            <!-- 用户信息 -->
            <view class="comment_user">
                <!-- 用户头像 -->
                <view class="user_icon"><image mode="widthFix" :src="item.user.avatar"></image></view>
                <!-- 用户名成 -->
                <view class="user_name">
                    <view class="user_nickname">{{item.user.name}}</view>
                    <view class="user_time">{{item.cnTime}}</view>
                </view>
                <!-- 用户徽章 -->
                <view class="user_badge">
                    <image
                    v-for="item2 in item.user.title"
                    :key="item2.icon"
                    :src="item2.icon"
                    ></image>
                </view>
            </view>
            <!-- 评论区数据 -->
            <view class="comment_desc">
                <view class="comment_content">{{item.content}}</view>
                <view class="comment_like">
                    <text class="iconfont icon-dianzan">{{item.size}}</text>
                </view>
            </view>
            </view>
        </view>
    </view>
    <!-- 最新评论 -->
     <view class="comment new" v-if="comment.length">
        <view class="comment_title">
            <text class="iconfont icon-ziyuan"></text>
            <text class="comment_text">最新评论</text>
        </view>
        <view class="comment_list">
            <view class="commet_item"
            v-for="item in comment"
            :key="item.id"
            >
            <!-- 用户信息 -->
            <view class="comment_user">
                <!-- 用户头像 -->
                <view class="user_icon"><image mode="widthFix" :src="item.user.avatar"></image></view>
                <!-- 用户名成 -->
                <view class="user_name">
                    <view class="user_nickname">{{item.user.name}}</view>
                    <view class="user_time">{{item.cnTime}}</view>
                </view>
                <!-- 用户徽章 -->
                <view class="user_badge">
                    <image
                    v-for="item2 in item.user.title"
                    :key="item2.icon"
                    :src="item2.icon"
                    ></image>
                </view>
            </view>
            <!-- 评论区数据 -->
            <view class="comment_desc">
                <view class="comment_content">{{item.content}}</view>
                <view class="comment_like">
                    <text class="iconfont icon-dianzan">{{item.size}}</text>
                </view>
            </view>
            </view>
        </view>
    </view>
    <!-- 下载图片 -->
    <view class="download" @click="handleDownload">
      <view class="download_btn">下载</view>
    </view>
  </view>
</template>

<script>
import moment from "moment";
import swiperAction from "@/components/swiperAction"
//设置语言为中文
moment.locale("zh-cn");
export default {
  components:{
    swiperAction
  },
  data() {
    return {
      imgDetail: {},
      //专辑数据
      album: {},
      hot: {},
      comment: {},
      //图片的索引
      imgIndex:0
    };
  },
  onLoad() {
    console.log(getApp().globalData);
    const { imgIndex } = getApp().globalData;
   
    this.imgIndex=imgIndex
    // this.imgDetail.newThumb =
    //   this.imgDetail.thumb + this.imgDetail.rule.replace("$<Height>", 360);
   this.getData()
  },
  methods: {
    //给当前页面赋值
    getData(){
    const { imgList } = getApp().globalData;
    this.imgDetail = imgList[this.imgIndex];
    console.log(222222, this.imgDetail);
    this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();
    this.getComments(this.imgDetail.id);
    },
    //请求数据
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`,
      }).then((res) => {
        console.log(111, res);
        this.album = res.res.album;
        //给hot数组中的对象添加一个时间属性 xxx月前
        res.res.hot.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow())
         res.res.comment.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow())
        this.hot = res.res.hot;
        this.comment = res.res.comment;
      });
    },
    //滑动事件
    handleSwiperAction(e){
      console.log(e)
      /**
       * 1.左滑 imgIndex++
       * 2.右划 imgIndex--
       * 3.判断数组是否越界
       * 4.左滑e.drection==="left"&&this.imgIndex<imgList.length-1
       * 5.右滑e.drection==="reght"&&this.imgIndex>0
       */
      const { imgList } = getApp().globalData;
      if(e.direction==="left"&&this.imgIndex<imgList.length-1){
        //可以进行左滑加载下一页
        this.imgIndex++;
        this.getData()
      }else if(e.direction==="right"&&this.imgIndex>0){
        //右滑加载上一页
        this.imgIndex--;
        this.getData()
      }else{
        uni.showToast({
          title:"没有数据了",
          icon:"none"
        })
      }
    },
    //点击下载图片
   async handleDownload(){
      /**
       * uni.downloadFile
       * uni.saveImageToPhotosAlbum
       */
      await uni.showLoading({
        title:"图片正在下载"
      })
      //1将远程文件下载到小程序的内存中 tempFilePath
     const res=await uni.downloadFile({url:this.imgDetail.img})
     const {tempFilePath}=res[1]
     //2将小程序中的临时文件下载到本地
     const res2=await uni.saveImageToPhotosAlbum({
       filePath:tempFilePath
     })
     uni.hideLoading()
     uni.showToast({
       title:"下载成功"
     })
    }
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
}

.user_desc {
  .user_name {
    color: #000;
    font-weight: 600;
  }

  .user_time {
    color: #ccc;
    font-size: 24rpx;
    padding: 10rpx 0;
  }
}
// --------------------------
.user_rank {
  display: flex;
  height: 88rpx;
  border-bottom: 1rpx solid #eee;
  .rank {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    .icondianzan {
    }
  }

  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    .iconshoucang {
    }
  }
}
.high_img{
    image{
        border-bottom: 1rpx solid #eee;
    }
}
// ----------------------------
.album_wrap {
    padding: 20rpx;
  .album_title {
      padding: 10rpx 0;
  }

  .album_list {
    .album_item {
        display: flex;
        padding: 10rpx 0;
        border-bottom: 10rpx solid #eee;
      .album_cover {
          flex: 1;
        image {
            width: 180rpx;
            height: 180rpx;
        }
      }


      .album_info {
          flex: 3;
          padding-left:20rpx ;
          position: relative;
        .album_info_text {
            width: 100rpx;
            height: 50rpx;
            background-color: $color;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;

        }

        .album_name {
            padding: 10rpx 0;
            color: #888;
        }
        .iconfont{
            font-size: 40rpx;
            position: absolute;
            top: 50%;
            transform:translateY(-50%);
            right:10%;
            color: #000;

        }
      }
    }
  }
}
//--------------------------------------
.comment {
  .comment_title {
      padding: 15rpx;
    .iconfont {
        color: red;
        font-size: 40rpx;
    }

    .comment_text {
        font-weight: 600;
        font-size: 30rpx;
        color: #666;
        margin-left: 10rpx;
    }
  }

  .comment_list {
  .commet_item {
      border-bottom: 15px solid #eee;
      //用户信息
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
          display: inline-block;
            width: 40rpx;
            height: 40rpx;
        }
      }
    }

    .comment_desc {
        display: flex;
        padding: 30rpx 0;
      .comment_content {
          flex:1;
          padding-left:15% ;
          color: #000;
      }

      .comment_like {
          text-align: right;
        .iconfont.icon-dianzan {

        }
      }
    }
  }
}
}
.icon-ziyuan{
    color:aqua !important;
}

//下载
.download{
  height:100rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn{
    width: 90%;
    height:85%;
    background-color: $color;
    text-align: center;
    font-size: 40rpx;
    font-weight: 600;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;

  }
}
</style>