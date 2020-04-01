<template>
	<view class="video">
		<video :src="src" :controls="controls" :show-play-btn="false" 
			:style="{ height: height }" :loop="true" @waiting="waiting"
			:enable-progress-gesture="false" :objectFit="objectFit"
			:id="`video_${src}`" ref="`video_${src}`" class="video" @timeupdate="timeupdate">
		</video>
		<cover-view class="icon-view" v-if="!play&&!playFirst"><text class="icon" style="color:#FFF">&#xe896;</text></cover-view>
		<cover-image class="img" :style="{ height: height }" :src="poster" v-if="poster!=''&&playFirst"></cover-image>
		<cover-view class="top"></cover-view>
		<cover-view class="bottom"></cover-view>
		<cover-view class="progressBar" :style="{ width: barWidth }"></cover-view>
	</view>
</template>

<script>
	export default{ 
		props:{
			controls:{
				type:Boolean,
				default:false
			},
			src:{
				type:String,
				default:''
			},
			play:{
				type:Boolean,
				default:false
			},
			height:{
				type:String,
				default:''
			},
			width:{
				type:String,
				default:''
			},
			initialTime:{
				type:Number,
				default:0
			},
			gDuration:{
				type:Number,
				default:999
			}, //大概时长使进度条更准确
			objectFit:{
				type:String,
				default:'contain'
			},
			poster:{ //视频封面的图片
				type:String,
				default:''
			}
		},
		data(){
			return{
				time:0,
				duration:0,
				playFirst:true
			}
		},
		beforeCreate() {
			// #ifdef APP-NVUE
			var domModule = weex.requireModule('dom');
			domModule.addRule('fontFace', {
				'fontFamily': "texticons",
				'src': "url('/static/chunlei-video/text-icon.ttf')"
			});
			// #endif
		},
		mounted() {
			this.videoCtx = uni.createVideoContext(`video_${this.src}`,this)
			
		},
		methods:{
			timeupdate(event){
				this.duration = event.detail.duration
				if(!this.play) return
				if(this.time>=this.duration) this.time=0
				this.time = event.detail.currentTime
			},
			videoPlay(){
				if(this.play){
					this.videoCtx.play();
					if(this.playFirst){
						this.videoCtx.seek(this.startTime)
						this.playFirst = false
					} 
				}else{
					this.videoCtx.pause();
					this.$emit('pause',this.time)
				}
				
			},
			waiting(){
				
			}
		},
		watch:{
			play(newVal,oldVal){
				this.videoPlay()
			},
			startTime:{
				immediate: true,
				handler(newVal,oldVal){
					
					this.time = newVal
				}
			},
			gDuration:{
				immediate: true,
				handler(newVal,oldVal){
					
					this.duration = newVal
				}
			}
		},
		computed:{
			barWidth(){
				
				let width = this.time/this.duration*parseInt(this.width)
				
				return `${width}px`
			},
			startTime(){
				return this.initialTime
			}
		}
	}
</script>

<style scoped>
	/* #ifndef APP-PLUS-NVUE */
	@font-face {
		font-family: "texticons";
		src: url('~@/static/chunlei-video/text-icon.ttf') format('truetype');
	}
	/* #endif*/
	.video{
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		width: 750rpx;
		
		justify-content: center;
		align-items: center;
	}
	.img{
		position: absolute;
		
		width: 750rpx;
	}
	.icon-view{
		position: absolute;
	}
	.top{
		position: absolute;
		top:0;
		background-image: linear-gradient(to top , rgba(0,0,0,0) , rgba(0,0,0,0.7));
		width: 750rpx;
		height: 300rpx;
	}
	.icon{
		opacity: 0.6;
		font-size: 42px;
		color: #fff;
		/* #ifndef APP-PLUS-NVUE */
		font-family: "texticons";
		/* #endif*/
		font-family: texticons;
	}
	.bottom{
		position: absolute;
		bottom: 0;
		background-image: linear-gradient(to top , rgba(0,0,0,0.7) , rgba(0,0,0,0));
		width: 750rpx;
		height: 300rpx;
	}
	.progressBar{
		border-radius: 2upx;
		height: 4upx;
		background-color: #FFFFFF;
		z-index: 999999;
		position: absolute;
		left: 0;
		bottom: 40upx;
		//#ifndef APP-PLUS-NVUE
		animation: flicker 4s linear infinite;
		animation-direction:alternate;
		//#endif
	}
	//#ifndef APP-PLUS-NVUE
	@keyframes flicker {
		0% { box-shadow:0 0 0 #FFFFFF; }
	     /** 暂停效果 */
		10% { box-shadow:0 0 2upx #FFFFFF; }
	    50% { box-shadow:0 0 10upx #FFFFFF; }
	    60% { box-shadow:0 0 12upx #FFFFFF; }
	    90% { box-shadow:0 0 18upx #FFFFFF; }
	    100% { box-shadow:0 0 20upx #FFFFFF; }
	
	}
	//#endif
</style>
