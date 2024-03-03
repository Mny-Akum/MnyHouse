<template>
	<view>
		<scroll-view class="upNavigationBar" scroll-x="true" show-scrollbar="false">
			<view v-for="n in upNavigationBarList" @click="updateSelectedID(n.id)" :key="n.id" :class=" selectedID==n.id?'active':'' ">{{n.name}}</view>
		</scroll-view>
		<view class="newsList">
			<view v-if="newsList.length == 0 && !newsRequestParameter.updateNews">暂无</view>
			<view v-for="news in newsList" :key="news.id" @click="toDetail(news.id)">
				<ItemPage :news="news"></ItemPage>
			</view>
			<view v-if="!newsRequestParameter.updateNews && newsRequestParameter.newsPage !=1" class="noMore">--&emsp;暂无更多&emsp;--</view>
		</view>
	</view>
</template>

<script>
	import ItemPage from "../../components/ItemPage/ItemPage.vue"
	export default {
		components:{
			ItemPage
		},
		data() {
			return {
				title: 'Hello',
				selectedID:"50",
				newsRequestParameter:{
					newsPage:1,
					num:8,
					updateNews:true
				},
				upNavigationBarList:[
					{
						name:"国内",
						id:"50"
					},{
						name:"国际",
						id:"51"
					},{
						name:"体育",
						id:"52"
					},{
						name:"科技",
						id:"53"
					},{
						name:"时尚",
						id:"54"
					},{
						name:"游戏",
						id:"55"
					}
				],
				newsList:[]
			}
		},
		onLoad() {

		},
		methods: {
			updateSelectedID(id){
				this.newsRequestParameter.updateNews = true
				this.newsRequestParameter.newsPage = 1
				
				if(this.selectedID != id){
					this.newsRequestParameter.newsPage = 1
					uni.showLoading({})
					uni.request({
						"url":"https://ku.qingnian8.com/dataApi/news/newslist.php",
						"data":{
							cid:id,
							num:this.newsRequestParameter.num
						},
						"success":({data})=>{
							if(data.length < this.newsRequestParameter.num){
								this.newsRequestParameter.updateNews = false
							}
							this.newsList = data
							uni.hideLoading()
						}
					})
				}
				
				this.selectedID = id
			},
			toDetail(id){
				var browsingHistory = uni.getStorageSync("browsingHistory")
				if(browsingHistory){
					
					browsingHistory.map((item,index)=>{
						if(item.id == id && item.cid == this.selectedID){
							browsingHistory.splice(index,1)
						}
					})
					
					browsingHistory.unshift({
						id,
						cid:this.selectedID,
						Btime:(new Date()).getTime()
					})
					
					uni.setStorageSync("browsingHistory",browsingHistory)
				}else{
					uni.setStorageSync("browsingHistory",[{
						id,
						cid:this.selectedID,
						Btime:(new Date()).getTime()
					}])
				}
				uni.navigateTo({
					url:`../../pages/detailPage/detailPage?newsID=${id}`,
				})
			}
		},
		
		
		onLoad(){
			uni.showLoading({})
			uni.request({
				"url":"https://ku.qingnian8.com/dataApi/news/newslist.php",
				"data":{
					cid:this.selectedID,
					num:this.newsRequestParameter.num
				},
				"success":({data})=>{
					this.newsList = data
					uni.hideLoading()
				}
			})
		},
		
		
		onReachBottom(){	//生命周期
			if(!this.newsRequestParameter.updateNews) return
			this.newsRequestParameter.newsPage++
			uni.showLoading({})
			uni.request({
				"url":"https://ku.qingnian8.com/dataApi/news/newslist.php",
				"data":{
					cid:this.selectedID,
					page:this.newsRequestParameter.newsPage,
					num:this.newsRequestParameter.num
				},
				"success":({data})=>{
					if(data.length < this.newsRequestParameter.num){
						this.newsRequestParameter.updateNews = false
					}
					this.newsList.push(...data)
					uni.hideLoading()
				}
			})
		}
	}
</script>

<style lang="scss">
	.upNavigationBar{
		//滚动条隐藏设置？？
		background-color: #f5f5f5;
		white-space: nowrap;
		top: 88rpx;
		height: 100rpx;
		position: sticky;
		z-index: 1;
		view{
			display: inline-block;
			width: 140rpx;
			height: 100rpx;
			font-size: 38rpx;
			line-height: 100rpx;
			color: #515151;
			text-align: center;
			&.active{
				color: #00d37f;
			}
		}
	}
	.newsList{
		.noMore{
			text-align: center;
			margin: 50rpx;
		}
	}
</style>
