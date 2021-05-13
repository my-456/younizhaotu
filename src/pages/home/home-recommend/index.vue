<template>
  <scroll-view @scrolltolower="handleToLower" class="scroll" scroll-y  v-if="recommends.length>0">
    <!-- 推荐 开始 -->
    <navigator class="recommend_wrap" :url="`/pages/album/album?id=${item.id}`">
      <view class="recommend_item"
      v-for="item in recommends"
      :key="item.id"
      >
        <image mode="widthFix" :src="item.thumb"></image>
      </view>
    </navigator>
    <!-- 推荐结束 -->

    <!-- 月份开始 -->
  <view class="months_wrap">
      <view class="months_title" >
          <view class="months_title_info">
              <view class="moneths_info">
                  <text>{{months.MM}}</text>
                  /{{months.DD}} 月
              </view>
              <view class="months_text">{{months.title}}</view>
          </view>
          <view class="months_title_more">更多 ></view>
      </view>
       <view class="months_content">
        <view class="months_item" v-for="(item,index) in months.items" :key="item.id">
        <go-detail :list="months.items" :index="index">
        <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
        </go-detail>
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
          <view class="hot_item" v-for="(item,index) in hots" :key="item.id">
            <go-detail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb"></image>
            </go-detail>
          </view>
      </view>
  </view>
     <!-- 热门结束 -->
     <view class="hasMore" :v-show="!this.end">到底了，没有数据了！</view>
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail"
export default {
  components:{
    goDetail
  },
    data(){
      return{
        months:{},
        data:{
          //需要取多少条
          limit:30,
          //关键字
          order:"hot",
          //要跳过几条
          skip:0
        },
        //是否还有下一页
        hasMore:true,
        end:true,
        recommends:[],
        hots:[]
      }
    },
  mounted(){
  uni.setNavigationBarTitle({title:"推荐"})
  this.getList()
   
  },
  methods:{
    handleToLower(){
      if(this.hasMore==true){
        this.data.skip+=this.data.limit 
        this.getList()
      }else{
        console.log("到底了")
        this.end=false
      }
      
    },
    getList(){
       this.request({
      url:"http://157.122.54.189:9088/image/v3/homepage/vertical",
      data:this.data
    }).then((res)=>{
      console.log(res)

      if(res.res.vertical.length===0){
        this.hasMore=false;
      }
      if(this.recommends.length===0){
      //头部推荐模块
      this.recommends=res.res.homepage[1].items;
      //月份模块
      this.months=res.res.homepage[2];
      //将时间戳改成 18号/月
      this.months.MM=moment(this.months.stime).format("MM")
      this.months.DD=moment(this.months.stime).format("DD")
      console.log(this.months)
      }
      //获取热门数据的列表
      this.hots=[...this.hots,...res.res.vertical]
      console.log(this.hots)
    })
    }
    
  }
}
</script>

<style lang="scss" scoped>
.scroll{
 height:100vh ; 
 .hasMore{
   height: 40rpx;
   width: 100%;
   text-align: center;
   font-size: 34rpx;
   background-color: $color;
 }
}
.recommend_wrap {
  //flex布局
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width:50%;
    img {
      width:50%;
      border: 3rpx solid #fff;
    }
  }
}

.months_wrap {
  .months_title {
    display: flex;
    justify-content: space-between;
    margin: 20rpx 20rpx;
    .months_title_info {
      color: $color;
      font-size: 30rpx;
       font-weight: 600;
       display: flex;
      .moneths_info {
        text {
          font-size: 36rpx;
         
        }
      }

      .months_text {
        font-size: 34rpx;
        color: #666;
        margin-left: 30rpx;
      }
    }

    .months_title_more {
      font-size: 34rpx;
      color: $color;
    }
  }

  .months_content {
    display: flex;
    flex-wrap: wrap;
    .months_item{
      flex:33.3%;
      //width:50%;
      border: 5rpx solid #fff;
    }
  }
}

.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      border-left: 20rpx solid $color;
      font-size: 34rpx;
      font-weight: 600;
      padding-left:20rpx ;
    }
  }

.hots_content {
  display: flex;
  flex-wrap: wrap;
  .hot_item {
    width: 33.33%;
    border:5rpx solid #fff;
      image {

      }
    }
  }
}
</style>