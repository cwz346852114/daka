<template>
	<view class="productList">
		<view v-for="(item,index) in myProductList">
			<view class="item">
				<view class="left">
					<image class="img" :src="item.img" alt="" />
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
					<view @click="closeProduct(item,index)" style="color: cornflowerblue;">退款商品</view>

				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="myProductList.length==0">暂无商品数据</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				count: 0,
				myProductList: [],
				messageList: []
			}
		},
		onLoad() {},
		onShow() {
			this.getList()
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
			closeProduct(item, index) {
				uni.showToast({
					title: "退款成功",
					icon: 'none'
				})
				if (item.num > 1) {
					item.num = item.num - 1
				} else {
					this.myProductList.splice(index, 1)
				}

				this.messageList.unshift({
					title: '商品退款',
					content: `尊敬的用户您好，您已退款商品${item.name}`,
					type: '退款',
					time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`
				})

				this.count = this.count + item.price
				uni.setStorage({
					key: 'messageList',
					data: this.messageList,

				})
				uni.setStorage({
					key: 'count',
					data: this.count,

				})
				uni.setStorage({
					key: 'myProductList',
					data: this.myProductList,

				})

			},
			shiyong(item, index) {
				if (item.num > 1) {
					item.num = item.num - 1
				} else {
					this.myProductList.splice(index, 1)
				}
				uni.showToast({
					title: "使用成功",
					icon: 'none'
				})



				this.messageList.unshift({
					title: '商品使用',
					content: `尊敬的用户您好，您已使用商品${item.name}`,
					type: '使用',
					time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`
				})
				uni.setStorage({
					key: 'myProductList',
					data: this.myProductList,
				})
				uni.setStorage({
					key: 'messageList',
					data: this.messageList,

				})
			},


			getList() {
				uni.getStorage({
					key: 'myProductList',
					success: (res) => {
						this.myProductList = res.data;
						console.log(this.myProductList)
						console.log(this.myProductList)
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
				uni.getStorage({
					key: 'count',
					success: (res) => {
						this.count = res.data;
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