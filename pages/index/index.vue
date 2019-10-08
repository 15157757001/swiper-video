<template>
    <div class="page">
		<swiper
			class="swiper"
			:vertical="true"
			@change="changeCurrent"
			:style="{ height: height }"
			:current="index">
			<swiper-item v-for="(item, idx) in videoList" :key="idx" class="swiper-item" @click="clickVideo()">
				<div class="video-box">
					<chunlei-video class="video" :src="item.src"  :height="height" :width="width" 
						:play="item.flag" v-if="Math.abs(index-idx)<=1"
						:initialTime="item.initialTime" @pause="pauseVideo"
					>
					</chunlei-video>
					<cover-view class="cover-view-left">
						<!-- #ifdef APP-PLUS-NVUE-->
						<text class="left-text">{{item.content}}</text>
						<!-- #endif -->
						<!-- #ifndef APP-PLUS-NVUE-->
						{{item.content}}
						<!-- #endif -->
					</cover-view>
					<cover-view class="cover-view-right">
						<cover-image :src="item.avater" class="avater img" @click.stop="tapAvater"></cover-image>
						<cover-image :src="item.check?'../../static/aixinRed.png':'../../static/aixin.png'" class="img" @click.stop="tapLove"></cover-image>
						<cover-image src="../../static/xiaoxi.png" class="img" @click.stop="tapMsg"></cover-image>
						<cover-image src="../../static/share-fill.png" class="img" @click.stop="tapShare"></cover-image>
					</cover-view>
				</div>
			</swiper-item>
		</swiper>
    </div>
</template>
<script>
	import chunleiVideo from '../../components/chunlei-video/chunlei-video.vue'
    export default { 
		components:{
			chunleiVideo
		},
        data() {
            return {
				sysheight:0,
				videoList:[
					{
						src:'https://dcloud-img.oss-cn-hangzhou.aliyuncs.com/guide/uniapp/%E7%AC%AC1%E8%AE%B2%EF%BC%88uni-app%E4%BA%A7%E5%93%81%E4%BB%8B%E7%BB%8D%EF%BC%89-%20DCloud%E5%AE%98%E6%96%B9%E8%A7%86%E9%A2%91%E6%95%99%E7%A8%8B@20181126.mp4',
						content:'456',
						flag:false,
						check:false,
						avater:'../../static/logo.png',
						initialTime:0
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167057&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'123',
						flag:false,
						check:true,
						avater:'http://img.kaiyanapp.com/255365dbfc2622930eb0cdb33e43abf0.jpeg?imageMogr2/quality/60/format/jpg',
						initialTime:0
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167805&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'789',
						flag:false,
						check:false,
						avater:'http://img.kaiyanapp.com/255365dbfc2622930eb0cdb33e43abf0.jpeg?imageMogr2/quality/60/format/jpg',
						initialTime:0
					},
				],
				height:'667px',
				index:0,
				width:'',
				oldIndex:0
			}
        },
		created(){
			//#ifdef APP-PLUS
			plus.screen.lockOrientation("portrait-primary")
			//#endif
			this.sysheight = uni.getSystemInfoSync().windowHeight
			this.height = `${this.sysheight}px` 
			let width = uni.getSystemInfoSync().windowWidth 
			this.width = `${width}px` 
		},
		async mounted() {
			await this.pushVideoList()
			this.videoPlay(this.index)
		},
        methods: {
			changeCurrent(e){
				this.index = e.detail.current;
				for (let item of this.videoList) {
					item.flag = false
				}
				this.videoList[this.index].flag = !this.videoList[this.index].flag
			},
			pushVideoList(){
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: 'https://api.apiopen.top/videoRecommend?id=127396',
						success: (res) => {
							let videoGroup = []
							for (let item of res.data.result) {
								if(item.type == 'videoSmallCard'){
									videoGroup.push({
										src:item.data.playUrl,
										content:item.data.description,
										flag:false,
										check:false,
										avater:item.data.author.icon,
										initialTime:0
									})
								}
							}
							this.videoList = [...this.videoList,...videoGroup]
							this.videoList = [...this.videoList,...videoGroup]
							this.videoList = [...this.videoList,...videoGroup]
							resolve()
						}
					})
				}) 
				return promise
			},
			tapLove(){
				this.videoList[this.index].check = !this.videoList[this.index].check
				this.videoList = [...this.videoList]
			},
			pauseVideo(val){
				if(typeof this.videoList[this.index].initialTime !='undefined') this.videoList[this.index].initialTime = val
			},
			tapAvater(){
				uni.showToast({
					icon:'none',
					title:`点击索引为${this.index}的头像`
				})
			},
			tapMsg(){
				uni.showToast({
					icon:'none',
					title:`查看索引为${this.index}的评论`
				})
			},
			tapShare(){
				uni.showToast({
					icon:'none',
					title:`分享索引为${this.index}的视频`
				})
			},
			videoPlay(index){
				let promise = new Promise((resolve,reject)=>{
					resolve()
				})
				promise.then(res=>{
					this.videoList[index].flag = !this.videoList[index].flag
				})
			},
			clickVideo(){
				this.videoList[this.index].flag = !this.videoList[this.index].flag
			},
			pauseVideo(val){
				if(typeof this.videoList[this.oldIndex].initialTime !='undefined') this.videoList[this.oldIndex].initialTime = val
			},
        },
		watch:{
			index(newVal,oldVal){
				this.oldIndex = oldVal
			}
		}
    }
</script>
<style scoped>
	.swiper{
		background-color: #000;
		display: flex;
	}
	.swiper-item{
		position: relative;
	}
    .video {
		
    }
	.video-box{
		flex: 1;
	}
	.cover-view-center{
		position: absolute;
		justify-content: center;
		align-items: center;
		opacity: 0.1;
		z-index: 999;
	}
	.cover-view-left{
		position: absolute;
		margin-left: 10upx;
		width: 400upx;
		bottom: 100upx;
		z-index: 9999;
		font-size: 14px;
		color: #FFFFFF;
		//#ifndef APP-PLUS-NVUE
		white-space: pre-wrap;  
		text-overflow:ellipsis;
		overflow:hidden;
		//#endif
	}
	.left-text{
		font-size: 14px;
		color: #FFFFFF;
	}
	.avater{
		border-radius: 40upx;
	}
	
	.cover-view-right{
		position: absolute;
		bottom: 80upx;
		margin-right: 20upx;
		right: 0;
		//#ifndef APP-PLUS-NVUE
		display: flex;
		flex-direction: column;
		//#endif
		z-index: 9999;
	}
	
	.img{
		height: 80upx;
		width: 80upx;
	
	}
	.page{
		
	}
	
</style>