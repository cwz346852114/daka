<template>
	<view class="productList">
		<view v-for="(item,index) in myJiang">
			<view class="item">
				<view class="left">
					<image class="img" v-if="index%3==0" src="@/static/product/beiguo.png" alt="" />
					<image class="img" v-if="index%3==1" src="@/static/product/paigu.png" alt="" />
					<image class="img" v-if="index%3==2" src="@/static/product/jidan.png" alt="" />
				</view>
				<view class="center">

					<view>{{item.name}}</view>
					<!-- <view>编号{{item.code}}</view> -->
					<view>
						<view>数量：{{item.num}} 个</view>
						<!-- <view>{{item.count}}</view> -->
					</view>

				</view>
				<view class="right">
					<view @click="shiyong(item,index)" style="color: cornflowerblue;" class="mb-10">使用</view>
					
				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="myJiang.length==0">暂无奖品数据</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				myJiang: [],
				messageList: []
			}
		},
		onLoad() {},
		onShow() {
			this.init()
			uni.getStorage({
				key: 'count',
				success: function(res) {
					this.count = res.data;
				},
				fail: function(err) {
					console.log(err)
				}
			});
		},

		methods: {
		
			shiyong(item, index) {
				if (item.num > 1) {
					item.num = item.num - 1
				} else {
					this.myJiang.splice(index, 1)
				}
				uni.showToast({
					title: "使用成功",
					icon: 'none'
				})
				
				
			
				this.messageList.unshift({
					title: '奖品使用',
					content: `尊敬的用户您好，您已使用奖品${item.name}`,
					type: '使用',
					time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`
				})
				uni.setStorage({
					key: 'myJiang',
					data: this.myJiang,
				})
				uni.setStorage({
					key: 'messageList',
					data: this.messageList,
				
				})
			},


			init() {
				uni.getStorage({
					key: 'myJiang',
					success: (res) => {
						this.myJiang = res.data;
					},
					fail: function(err) {
						console.log(err)
					}
				});
				uni.getStorage({
					key: 'messageList',
					success: (res) => {
						this.messageList = res.data;

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
		height: calc(100vh - 90px);

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
				}
			}

			.center {
				flex: 1;
			}

			.right {}
		}
	}
</style>