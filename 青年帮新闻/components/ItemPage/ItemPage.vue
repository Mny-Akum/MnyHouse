<template>
	<view class="newsStyle">
		<view class="newsImage"><image :src="data.picurl"></image></view>
		<view class="newsInfo">
			<view class="newsTitle">{{data.title}}</view>
			<view class="newsBottom">
				<text v-if="!data.Btime">{{data.author}}&emsp;&nbsp;{{data.hits}}浏览</text>
				<text class="BtimeS" v-if="data.Btime">浏览时间：{{data.Btime}}</text>
			</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		name:"ItemPage",
		data() {
			return {
				data:{}
			};
		},
		props:["news","requestData"],
		created(){
			
			if(!this.news){
				uni.showLoading({})
				uni.request({
					"url":"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:{
						cid:this.requestData.cid,
						id:this.requestData.id
					},
					
					success:(res)=>{
						var time = new Date(this.requestData.Btime)
						this.data = {
							...res.data,
							Btime:`${time.getFullYear()}-${time.getMonth()}-${time.getDate()} ${time.getHours()}:${time.getMinutes()}:${time.getSeconds()}`
						}
						uni.hideLoading()
					}
				})
			}else{
				this.data = this.news
			}
		}
	}
</script>

<style lang="scss">
	.newsStyle{
		height: 180rpx;
		margin: 45rpx 25rpx 0;
		display: flex;
		.newsImage{
			margin-right: 25rpx;
			height: 180rpx;
			width: 280rpx;
			image{
				height: 180rpx;
				width: 280rpx;
			}
		}
		.newsInfo{
			position: relative;
			.newsTitle{
				font-size: 32rpx;
				height: 80rpx;
				text-overflow: ellipsis;
				word-break: break-all;
				line-height: 40rpx; /* 行高，根据容器高度和期望显示的行数调整 */  
				overflow: hidden; /* 隐藏超出容器的内容 */  
				display: -webkit-box; /* 将文本视为弹性盒子布局 */  
				-webkit-line-clamp: 2; /* 限制显示的行数为2行 */  
				-webkit-box-orient: vertical; /* 设置盒子的方向为垂直 */  
				text-overflow: ellipsis; /* 文本溢出时显示省略号 */  
				white-space: normal; /* 允许文本正常换行 */  
				word-wrap: break-word; /* 允许在单词内换行 */  
			}
			.newsBottom{
				position: absolute;
				bottom: 0;
				font-size: 28rpx;
				color: #aaa;
				.BtimeS{
					font-size: 25rpx;
				}
			}
		}
	}
</style>