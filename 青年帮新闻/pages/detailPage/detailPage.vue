<template>
	<view class="newsBox">
		<view class="newsTitle">{{newsDetail.title}}</view>
		<view class="authorInfoStyle">
			<view>编辑：{{newsDetail.author}}</view>
			<view>发布日期：{{releaseTime}}</view>
		</view>
		<view class="content" v-html="newsDetail.content"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				newsDetail : {},
				releaseTime:""
			};
		},
		computed:{
		},
		beforeCreate(){
			var a 
			uni.request({
				"url":"https://ku.qingnian8.com/dataApi/news/detail.php",
				data:{
					cid:this.selectedID,
					id:this.$route.query.newsID
				},
				
				success:(res)=>{
					res.data.content = res.data.content.replace(/img src/g,"img width='100%' src")
					//正则表达式
					var a = res.data.posttime * 1000 
					var time = new Date(a)
					this.releaseTime = `${time.getFullYear()}-${time.getMonth()}-${time.getDate()}
					${time.getHours()}:${time.getMinutes()}:${time.getSeconds()}`
					this.newsDetail = res.data
					uni.setNavigationBarTitle({
						title: res.data.title
					})
				}
			})
		}
	}
</script>

<style lang="scss">
	.newsBox{
		padding: 25rpx;
		.authorInfoStyle{
			padding: 0 30rpx;
			height: 70rpx;
			font-size: 26rpx;
			line-height: 70rpx;
			display: flex;
			color: #555;
			background-color: #f5f5f5;
			justify-content: space-between;
			margin: 40rpx 0;
		}
		.newsTitle{
			font-size: 45rpx;
		}
	}
</style>
