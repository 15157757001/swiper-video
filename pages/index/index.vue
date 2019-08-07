<template>
    <view class="page">
		<view class="video-view" :style="containerStyle">
			<video class="video" :id="`video_${index}`" :key="index" :data-id = "index"
				:src="item.src" controls v-for="(item,index) in videoList"
				@tapVideo="videoPlay(index)"
				@touchstart="ListTouchStart" @touchmove="ListTouchMove" @touchend="ListTouchEnd">
				<cover-view class="cover-view-left">
					{{item.content}}
					<input placeholder="说说你的看法" /> 
				</cover-view>
				<cover-image class="avater img" @tap.stop="tapAvater" :src="item.avater"></cover-image>
				<cover-image class="aixin img" @tap.stop="tapLove" :src="item.check?'../../static/aixinRed.png':'../../static/aixin.png'"></cover-image>
				<cover-image class="xiaoxi img" @tap.stop="tapMsg" src="../../static/xiaoxi.png"></cover-image>
				<cover-image class="share img" @tap.stop="tapShare" src="../../static/share-fill.png"></cover-image>
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
						flag:false,
						check:false,
						avater:'http://img.kaiyanapp.com/255365dbfc2622930eb0cdb33e43abf0.jpeg?imageMogr2/quality/60/format/jpg'
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167057&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'123',
						flag:false,
						check:true,
						avater:'http://img.kaiyanapp.com/255365dbfc2622930eb0cdb33e43abf0.jpeg?imageMogr2/quality/60/format/jpg'
					},
					{
						src:'http://baobab.kaiyanapp.com/api/v1/playUrl?vid=167805&resourceType=video&editionType=default&source=aliyun&playUrlType=url_oss',
						content:'789',
						flag:false,
						check:false,
						avater:'http://img.kaiyanapp.com/255365dbfc2622930eb0cdb33e43abf0.jpeg?imageMogr2/quality/60/format/jpg'
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
				uni.$on('tapLove', ()=>{
					if(this.scroll) return
					this.tapLove()
				})
				uni.$on('tapMsg', ()=>{
					if(this.scroll) return
					this.tapMsg()
				})
				uni.$on('tapShare', ()=>{
					if(this.scroll) return
					this.tapShare()
				})
				uni.$on('tapCover', ()=>{
					if(this.scroll) return
					this.videoPlay(this.id)
				})
				uni.$on('tapAvater', ()=>{
					if(this.scroll) return
					this.tapAvater()
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
								//限制5条
								if(item.type == 'videoSmallCard'&&videoGroup.length<5){
									videoGroup.push({
										src:item.data.playUrl,
										content:item.data.description,
										flag:false,
										check:false,
										avater:item.data.author.icon
									})
								}
							}
							this.videoList = [...this.videoList,...videoGroup]
							resolve()
						}
					})
				}) 
				return promise
			},
			tapLove(){
				this.videoList[this.id].check = !this.videoList[this.id].check
				this.videoList = [...this.videoList]
			},
			tapAvater(){
				uni.showToast({
					icon:'none',
					title:`点击索引为${this.id}的头像`
				})
			},
			tapMsg(){
				uni.showToast({
					icon:'none',
					title:`查看索引为${this.id}的评论`
				})
			},
			tapShare(){
				uni.showToast({
					icon:'none',
					title:`分享索引为${this.id}的视频`
				})
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
<style lang="scss" scoped>
	@font-face {
		font-family: 'iconfont';
		src:url('data:application/x-font-woff2;charset=utf-8;base64,d09GMgABAAAAAAOUAAsAAAAAB5QAAANFAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHEIGVgCDHAqCUII2ATYCJAMQCwoABCAFhG0HRRulBsiuMTaGWU9MqV0Z10ZoxoS8XP3eZRMokWWCePhaQ9/f/XfZ3AWpVQyWx1dVIsgKTaDrTOoqHI5nIzJkl80GUDWRXR5nTgsFZJcWRPkAO3RfpwtouLq4wWWBLba8TjrxgpONCT1xs8YGKcvCYQEVaOdzAS2QTWMDRRxYv/gbIGAwtE7u+4kCDkRCi+SVYXnvDfg/x1KvSiC/3bVLVFo3H56eHbfdO7CI3kYDHsgGMuGhbtBdxKSoi5cJDHYLQlxRiqqAiQRmBeKpo5IBk4pVDpSGJvQdG1N8BFp5xUeAD/zn4z8igqKrwMKr97OVIO2Xz0fT7/xEEZBt50awjYojgCQednpv05fxI/QN2XAD5wBNU9iqR/poDw+OFGhjMNFQFv/zoucUT4EzdqLyi8MsCr8kjS6dZstv4KIEDT1uA54DlC08IcWyE/lHb7UMv3h9pW5Xvf4d3b9xPvqtvnnTYnVvCG0jW1b3SxDjl/OE940X1XU1ctF5Xi0qal/b/Cfntyp8wJcY2rv6oqqWz42gvrpq1mdaXpL26xdemEfqN1R4/X0cTlVYVQRMpMxA1w90dSoyMuwGFjIyYdnC/LwxzQqmkiwvI6F9O2NhnZP8EsDBs2Lz/LGkv5b4esEYw7RW7ey//8W/P+HLwDM5xFVmVrUDFsCOui1TqJ8FQ33Nx7/ndxz+uzXJbNL/Nib4EXeJ+3ekZdJ3LJss+xn9koAMFcCWmfpkcGnhSyeNWQoGA35BmgVftjAu32cyNMsaigm7oGr2IBN7BDqjM9BrzsPgsNzto0U8fZGTOKTPgjBnC4oZ21DNeYxM7GforPgLvblgGFwL+wNH+3KozBTJRKSE8iaoUTA0Rg7RTJRWIFUdKeNNBafWIF7QhGA4L9ZTuYhG/BZLhHpVhChiiHmGgjnWdYgkGcjyjBYpxBC1KLIJoYLZ9UkhCoYCZi7jITIiRAmSawJpKDBomDYWmTW+vwKiUockww9UzYRrIDyBZnAoXIiwCchcLT2p6lU+IqinEkEkwiCMx6BAOdY4hERiQGz3OC1EQRRCvSDKShAKa+Gp2pDj9dQTboIBWJxRokZGp7vlNGpoI0yQMaPNBFvGo2CsIUkAAAAAAA==') format('woff2');
		font-style: normal;
	}
	
	
	.iconfont {
	  font-family: "iconfont" !important;
	  font-size: 16px;
	  font-style: normal;
	  -webkit-font-smoothing: antialiased;
	  -moz-osx-font-smoothing: grayscale;
	}

	.icon-aixin:before {
	  content: "\e663";
	}

	.icon-xiaoxi:before {
	  content: "\e626";
	}

	.icon-share-fill:before {
	  content: "\e637";
	}


	
    .video {
    	width: 100%;
    	height: 100vh;
		position: relative;
		display: block;
    }
	.cover-view-left{
		position: absolute;
		margin-left: 10upx;
		width: 80%;
		color: #FFFFFF;
		bottom: 100upx;
		font-size: 14px;
		.left-content{
			width: 100%;
			white-space: pre-wrap;  
			text-overflow:ellipsis;
			overflow:hidden;
		}
		input{
			height: 50upx;
			border: 1px solid #FFFFFF;
			padding-left: 10upx;
			width: 400upx;
		}
	}
	.avater{
		position: absolute;
		bottom: 340upx;
		right: 0;
		margin-right: 10upx;
		border-radius: 40upx;
		height: 80upx;
		width: 80upx;
	}
	.aixin{
		position: absolute;
		bottom: 260upx;
		right: 0;
		margin-right: 10upx;
	}
	.xiaoxi{
		position: absolute;
		bottom: 180upx;
		right: 0;
		margin-right: 10upx;
	}
	.share{
		position: absolute;
		bottom: 100upx;
		right: 0;
		margin-right: 10upx;
	}
	.img{
		height: 80upx;
		width: 80upx;
	}
	.page{
		height: 100vh;
		overflow: hidden;
		
	}
	
</style>