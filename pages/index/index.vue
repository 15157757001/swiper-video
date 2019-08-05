<template>
    <view class="page">
		<view class="video-view" :style="containerStyle">
			<video class="video" :id="`video_${index}`" :key="index" :data-id = "index"
				:src="item.src" controls v-for="(item,index) in videoList"
				@touchstart="ListTouchStart" @touchmove="ListTouchMove" @touchend="ListTouchEnd">
				<cover-view class="cover-view">{{item.content}} </cover-view>
			</video>
		</view>
    </view>
</template>
<script>

    export default {
        data() {
            return {
                videoCtx: null,
				listTouchStartY: 0,
				listTouchDirection: null,
				containerStyle:'',
				sysheight:0,
				distance:0,
				scroll:false,
				id:0,
				videoList:[
					{
						src:'https://dcloud-img.oss-cn-hangzhou.aliyuncs.com/guide/uniapp/%E7%AC%AC1%E8%AE%B2%EF%BC%88uni-app%E4%BA%A7%E5%93%81%E4%BB%8B%E7%BB%8D%EF%BC%89-%20DCloud%E5%AE%98%E6%96%B9%E8%A7%86%E9%A2%91%E6%95%99%E7%A8%8B@20181126.mp4',
						content:'456',
						flag:false
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167057&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'123',
						flag:false
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167805&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'789',
						flag:false
					},
				],
			}
        },
		onLoad(){
			this.sysheight = uni.getSystemInfoSync().windowHeight
		},
        async mounted() {
			//#ifdef APP-PLUS
			const subNVue = uni.getSubNVueById('concat')  
			// 打开 nvue 子窗体  
			subNVue.show('slide-in-left', 100, ()=>{  
				uni.$on('ListTouchStart', (e)=>{
					this.listTouchStartY = e.changedTouches[0].pageY
				})
				uni.$on('ListTouchMove', (e)=>{
					this.ListTouchMoveY(e.changedTouches[0].pageY)
				})
				uni.$on('ListTouchEnd', ()=>{
					this.ListTouchEnd()
				})
			});  
			//#endif
        },
        methods: {
			pushVideoList(){
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: 'https://api.apiopen.top/videoRecommend?id=127397',
						success: (res) => {
							let videoGroup = []
							for (let item of res.data.result) {
								if(item.type == 'videoSmallCard'){
									videoGroup.push({src:item.data.playUrl,content:item.data.description,flag:false})
								}
							}
							this.videoList = [...this.videoList,...videoGroup]
							resolve()
						}
					})
				}) 
				return promise
			},
			// ListTouch触摸开始
			ListTouchStart(e) {
				this.listTouchStartY = e.touches[0].pageY
				
			},
			// ListTouch计算方向
			ListTouchMove(e) {
				this.ListTouchMoveY(e.touches[0].pageY)
			},
			ListTouchMoveY(Y){
				//该视频向上
				this.listTouchDirection = this.listTouchStartY - Y > 80 ? -1 : 0
				//该视频向下
				this.listTouchDirection = Y - this.listTouchStartY > 80 ?  1 : this.listTouchDirection
				const distance = this.distance + Y - this.listTouchStartY
				if(distance>0) return
				this.containerStyle = `transform:translate3d(0, ${distance}px, 0)`
				if(this.listTouchStartY - Y<10&&this.listTouchStartY-Y>-10){
					this.listTouchDirection = null
				}
			},
			// ListTouch计算滚动
			async ListTouchEnd(e) {
				if(this.scroll) return
				const id = this.id
				if(id == this.videoList.length-2){
					this.pushVideoList()
				}
				//点击事件
				if(!this.listTouchDirection&&this.listTouchDirection!=0){
					this.videoPlay(id)
					return
				}
				const destination = this.listTouchDirection*this.sysheight+this.distance
				//不允许滚动出列表
				if(destination>0||destination<-this.sysheight*(this.videoList.length-1)) return
				this.videoCtx = uni.createVideoContext(`video_${id}`);
				this.videoList[id].flag = !this.videoList[id].flag
				this.videoCtx.pause();
				//开始滚动
				await this.animate(destination, this.listTouchDirection)
				this.videoCtx = uni.createVideoContext(`video_${id-this.listTouchDirection}`);
				this.videoList[id-this.listTouchDirection].flag = !this.videoList[id-this.listTouchDirection].flag
				this.videoCtx.play();
				this.id = this.id-this.listTouchDirection
				this.listTouchDirection = null
			},
			videoPlay(id){
				if(!this.videoList[id].flag){
					this.videoCtx = uni.createVideoContext(`video_${id}`);
					this.videoCtx.play();
				}else{
					this.videoCtx = uni.createVideoContext(`video_${id}`);
					this.videoCtx.pause();
				}
				this.videoList[id].flag = !this.videoList[id].flag
			},
			animate(des, direc) {
				let { distance } = this
				let promise = new Promise((resolve,reject)=>{
					this.scroll = true
					const temp = setInterval(() => {
						if ((direc === -1 && des < distance) || (direc === 1 && des > distance)) {
							distance += 50 * direc
							this.containerStyle = `transform:translate3d(0, ${distance}px, 0)`
						} else {
							clearInterval(temp)
							distance = des
							this.distance = des
							this.containerStyle = `transform:translate3d(0, ${distance}px, 0)`
							this.scroll = false
							resolve()
						}
					}, 50)
				})
				return promise
			}
        },
		


    }
</script>
<style>
    .video {
    	width: 100%;
    	height: 100vh;
		position: relative;
		display: block;
    }
	.cover-view{
		position: absolute;
		height: 200upx;
		width: 100%;
		color: red;
		bottom: 200upx;
		font-size: 12px;
	}
	.page{
		height: 100vh;
		overflow: hidden;
		
	}
	
</style>