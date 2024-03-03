<template>
	<view>
		<view class="userPageTop">浏览历史</view>
		<view v-for="item in requestIDlist" :key="item.id" @click="toDetail(item.id,item.cid)" >
			<ItemPage :requestData="item"></ItemPage>
		</view>
	</view>
</template>

<script>
	import ItemPage from "../../components/ItemPage/ItemPage.vue"
	export default {
		data() {
			return {
			};
		},
		components:{
			ItemPage
		},
		methods:{
			toDetail(id,cid){
				var browsingHistory = uni.getStorageSync("browsingHistory")
				if(browsingHistory){
					
					browsingHistory.map((item,index)=>{
						if(item.id == id && item.cid == cid){
							browsingHistory.splice(index,1)
						}
					})
					
					browsingHistory.unshift({
						id,
						cid,
						Btime:(new Date()).getTime()
					})
					
					uni.setStorageSync("browsingHistory",browsingHistory)
				}else{
					uni.setStorageSync("browsingHistory",[{
						id,
						cid,
						Btime:(new Date()).getTime()
					}])
				}
				uni.navigateTo({
					url:`../../pages/detailPage/detailPage?newsID=${id}`,
				})
			}
		},
		onShow(){
			this.requestIDlist = uni.getStorageSync("browsingHistory")
		}
		
	}
</script>

<style lang="scss">
	.userPageTop{
		text-align: center;
		margin: 50rpx;
		font-size: 50rpx;
	}
</style>
