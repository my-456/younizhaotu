<template>
  <scroll-view @scrolltolower="handleTolower" scroll-y enable-flex class="video_main">
      <view
      class="video_item"
      v-for="item in videowp"
      :key="item.id"
      @click="goVideo(item)"
      >
        <image mode="widthFix" :src="item.img"></image>
      </view>
  </scroll-view>
</template>

<script>
export default {
    data(){
        return{
            videowp:[],
            hasNext:true
        }
    },
    props:{
        urlobj:Object,
    },
    watch:{
        urlobj(){
            console.log("数据发生变化了")
            console.log(this.urlobj) 
            this.videowp=[]
            //this.urlobj.params.skip=0
            this.getList()
        }  
    },
    mounted(){
      console.log(this.urlobj)  
      this.getList() 
    },
    methods:{
        getList(){
          this.request({
              url:this.urlobj.url,
              data:this.urlobj.params
          }).then(res=>{
              console.log(res)
              if(res.res.videowp===0){
                  this.hasNext=false
                  
                  return;
              }
            this.videowp=[...this.videowp,...res.res.videowp]
          })
      },
      handleTolower(){
          console.log("到底了")
          if(this.hasNext){
              this.urlobj.params.skip+=this.urlobj.params.limit
              this.getList()
          }else{
              uni.showToast({
                      title:"没有更多数据了",
                      icon:"none"
                  })
          }
      },
      goVideo(item){
          //1.将数据存到全局共享中
          getApp().globalData.video=item
          //2.页面跳转
          uni.navigateTo({
              url:"/pages/videoPlay/index"
          })

      }
    }
}
</script>

<style lang="scss" scoped>
    .video_main{
        display: flex;
        flex-wrap: wrap;
        height: calc(100vh - 36px);
        .video_item{
            width: 33.3%;
            border: 5rpx solid #fff;
            image{

            }
        }
    }
</style>