<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view> 
		<view class="info">
			<view class="author">
				编辑：{{detail.author}}
			</view>
			<view class="time">
				发布日期：{{detail.posttime}}
			</view>
		</view>
		<view class="content">
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
			严正声明：本站内容均采集于腾讯新闻，若有侵权请联系管理员（1958019964@qq.com）进行整改删除，本站采集的内容均不代表作者以及本站的观点，
			若有侵权请及时联系管理员，感谢你的支持。
		</view>
	</view>
</template>

<script>
	import {parseTime} from "../../utils/tool.js"
	export default {
		data() {
			return {
				option: null,
				detail: {}
			}
		},
		
		onLoad(e){
			console.log(e);
			this.option = e;
			this.getdetailData()
		},
		methods: {
			getdetailData() {
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:this.option,
					success: res => {
						console.log(res);
						res.data.posttime = parseTime(res.data.posttime)
						res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%"')
						
						this.detail = res.data
						
						this.savaHistory()
						
						uni.setNavigationBarTitle({
							title:this.detail.title
						})
					}
				})
			},
			savaHistory() {
				console.log("=========");
				let historyArr = uni.getStorageSync("historyArr") || []
				
				let item = {
					id: this.detail.id,
					classid: this.detail.classid,
					picurl: this.detail.picurl,
					title: this.detail.title,
					looktime:parseTime(Date.now())
				}
				let index = historyArr.findIndex(i => {
					return i.id == this.detail.id
				})
				
				if(index>=0) {
					historyArr.splice(index,1)
				}
				
				historyArr.unshift(item)
				historyArr = historyArr.slice(0,10)
				
				uni.setStorageSync("historyArr",historyArr)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.detail {
		padding: 30rpx 0;
		.title {
		font-size: 46rpx;
		color: #333;
		}
		.info {
			background: #dadce0;
			padding: 20rpx 20rpx;
			font-size: 25rpx;
			color: #666;
			margin: 40rpx 0;
			display: flex;
			justify-content: space-between;
		}
		.content {
			padding-bottom: 50rpx;
			// /deep/ img {
			// 	max-width: 100%;
			// }
		}
		.description {
			background-color: #fef0f0;
			font-size: 26rpx;
			padding: 20rpx;
			color: #F89898;
			line-height: 1.8em;
		}
	}
</style>
