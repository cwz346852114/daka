<template>
	<view class="productList">
		
		<view v-for="(item,index) in requestList">
			<view class="item">
				<view class="left">
					<image class="img"  src="@/static/product/renwu.png" alt="" />
			
				</view>
				<view class="center">

					<view>{{item.name}}</view>
					<!-- <view>任务编号：{{item.code}}</view> -->
					<view>
						<view>任务积分：{{item.price}} 分</view>
						<view>打卡次数：{{item.count}}/{{item.number}}</view>
						<!-- <view>{{item.count}}</view> -->
					</view>

				</view>
				<view class="right">
					<uni-tag @click="daka(item)" :disabled="item.count===item.number" text='打卡'></uni-tag>
					<!-- <uni-tag @click="chongzhiItem(item)"  text='重置打卡'></uni-tag> -->
				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="requestList.length==0">暂无任务数据</view>
		<view style="text-align: right;position: fixed;bottom: 120rpx; left:0;">		
		<uni-tag @click="chongzhidaka" text='重置打卡'></uni-tag>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				requestList: [],
				title: '新增',
				count: 0,
				time: '',
				isRu:false,
				index: 0,
		
			}
		},
		onShow() {
			this.getList()

		},
		methods: {
			chongzhiItem(){
				
			},
			chongzhidaka(){
				
				this.requestList.forEach(el=>{
					el.count = 0;
				})
				uni.setStorage({
					key: 'requestList',
					data: this.requestList
				})
			},
			daka(item) {
					if(this.isRu){
					uni.showToast({
						title:"请点击不要过于频繁",
						icon:"none"
					})
						return false;
					}
					this.isRu = true;
					item.count += 1;
					uni.showToast({
						title: '打卡成功',
						icon: 'none'
					})
					this.count += item.price
					uni.setStorage({
						key: 'count',
						data: this.count
					})
					uni.setStorage({
						key: 'requestList',
						data: this.requestList
					})
				
					setTimeout(()=>{
						this.isRu = false;
					},1000)

			},
			getList() {
				uni.getStorage({
					key: 'requestList',
					success: (res) => {
						this.requestList = res.data || []
					},
					fail: function(err) {
						console.log(err)
					}
				});
				uni.getStorage({
					key: 'count',
					success: (res) => {
						this.count = res.data || 0
					},
					fail: function(err) {
						console.log(err)
					}
				});
				uni.getStorage({
					key: 'requestList',
					success: (res) => {
						this.requestList = res.data;
						// if (this.myRequestList.length > 0) {
						// 	this.myRequestList = this.myRequestList.concat(this.requestList).reduce((pre,
						// 		cur) => {
						// 		let index = pre.findIndex(item => item.code === cur.code)
						// 		if (index === -1) {
						// 			pre.push(
						// 				cur
						// 			)
						// 		}
						// 		return pre
						// 	}, [])
						// } else {
						// 	this.myRequestList = JSON.parse(JSON.stringify(this.requestList))
						// }
						// this.getTime()

					},
					fail: function(err) {
						console.log(err)
					}
				});

			},

		}
	}
</script>

<style lang="scss" scoped>
	.productList {
		// height: calc(100vh - 90px);

		.addProduct {

			position: fixed;
			bottom: 0;
			left: 0;
			right: 0;
		}

		.item {
			display: flex;
			background-color: #fff;
			padding: 20rpx;
			margin: 20rpx;
			border-radius: 4px;
			align-items: center;

			.left {
				.img {
					width: 120rpx;
					height: 120rpx;
					margin-right: 20rpx;
				}
			}

			.center {
				flex: 1;
			}

			.right {}
		}
	}
</style>