<template>
	<view class="productList">
		<view v-for="(item,index) in productList">
			<view class="item">
				<view class="left">
					<image class="img" v-if="index%3==0" src="@/static/product/beiguo.png" alt="" />
					<image class="img" v-if="index%3==1" src="@/static/product/paigu.png" alt="" />
					<image class="img" v-if="index%3==2" src="@/static/product/jidan.png" alt="" />
				</view>
				<view class="center">

					<view>{{item.name}}</view>
				<view style="display: flex;">
					<!-- <view style="margin-right: 40rpx;">编号：{{item.code}}</view> -->
				<view>库存：{{item.number}}</view>
				</view>
					<view>
						<view>价格：{{item.price}} 分</view>
						<!-- <view>{{item.count}}</view> -->
					</view>

				</view>
				<view class="right">
					<view @click="pay(item,index)" style="color: cornflowerblue;">购买</view>

				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="productList.length==0">暂无商品数据</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				productList: [],
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
				success: (res) => {
					this.count = res.data;
				},
				fail: (err) => {
					console.log(err)
				}
			});
		},

		methods: {

			pay(item) {
				if (this.count > item.price) {
					// 积分变少 商品添加
					if(item.number<1){
						uni.showToast({
							title: "商品库存不足",
							icon: 'none'
						})
						return 
					}
					this.count = this.count - item.price;
					// 判断item在 this.myProductList中是否存在 如果存在 num +1；
					this.productList.forEach(el=>{
						if(item.code==el.code){
							el.number = el.number-1;
						}
					})
					if (this.myProductList.find(el => el.code == item.code)) {
						this.myProductList.forEach(el => {
							if (el.code == item.code) {
								el.num += 1;
							}
						})
					} else {
						this.myProductList.push({
							name: item.name,
							num: 1, //数量
							code: item.code,
							price: item.price
						})
					}
					this.messageList.unshift({
						title: '商品购买',
						content: `尊敬的用户您好，您已购买商品${item.name}`,
						type: '购买',
						time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`
					})
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
					uni.setStorage({
						key: 'productList',
						data: this.productList,
					
					})
					uni.showToast({
						title: "购买成功",
						icon: 'none'
					})
					// 添加提示
				} else {
					//
					uni.showToast({
						title: "积分不足",
						icon: 'none'
					})
				}
				console.log(this.count)

			},


			getList() {
				uni.getStorage({
					key: 'productList',
					success: (res) => {
						this.productList = res.data;
						console.log(this.productList)
					},
					fail: function(err) {
						console.log(err)
					}
				});
				uni.getStorage({
					key: 'myProductList',
					success: (res) => {
						this.myProductList = res.data;
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
						console.log(this.messageList)
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
		// margin-bottom: 50px;

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