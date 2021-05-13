<template>
  <view class="home_category">
      <navigator :url="`/pages/imageCategory/imageCategory?id=${item.id}`" class="cate_item" v-for="item in category" :key="item.id">
        <image mode="aspectFill" :src="item.cover"></image>
        <view class="cate_name">{{item.name}}</view>
      </navigator>
  </view>
</template>

<script>
export default {
  data(){
    return{
      category:[]
    }
  },
  mounted(){
  uni.setNavigationBarTitle({title:"分类"})
  this.getList()
},
methods:{
  getList(){
    this.request({
      url:"http://157.122.54.189:9088/image/v1/vertical/category"
    }).then(res=>{
      console.log(res)
      this.category=res.res.category;
    })
  }
}
}
</script>

<style lang="scss">
.home_category{
   display: flex;
  flex-wrap: wrap;
.cate_item {
  flex: 33.3%;
  position: relative;
  border: 5rpx solid #fff;
    image {
      height: 240rpx;

    }
    .cate_name {
      position: absolute;
      height: 50rpx;
      z-index: 99;
      margin-top: -45rpx;
      margin-left: 20rpx;
      font-size: 30;
      color: white;
    //css3渐变
    background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0,0,0,0));
    }
  }
  }
</style>