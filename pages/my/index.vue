<template>
	<view class="container">
		<view class="user">
			<image class="img" src="/static/img/man.png" alt="" />
			<view class="mb-10">
				{{userName}}
			</view>

			<view class="mb-10" style="margin-bottom: 20rpx;display: flex;justify-content: center;">
				<view style="display: flex;">
					<view>积分:</view>
					<view style="color: #E6A23C;">{{count}} </view>
				</view>
				<view @click="setCount" style="margin-left: 20rpx;cursor: pointer;">修改积分</view>
			</view>
		</view>
		<view>
			<!-- 	<uni-section class="mb-10" title="我的订单">

				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section> -->
			<uni-section @click="goTo('/pages/my/myProduct')" class="mb-10" title="我的物品">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section>
		<!-- 	<uni-section @click="goTo('/pages/my/myJiang')" class="mb-10" title="我的奖品">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section> -->
			<uni-section @click="goTo('/pages/message/index')" class="mb-10" title="消息通知">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section>
			<!-- <uni-section @click="goTo('/pages/choujiang/index')" class="mb-10" title="抽奖">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section> -->
			<uni-section @click="goTo('/pages/type/index')" class="mb-10" title="商品类型">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section>
			<uni-section @click="goTo('/pages/product/index?edit=1')" class="mb-10" title="商品管理">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section>
			<uni-section @click="goTo('/pages/request/index')" class="mb-10" title="任务管理">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section>
			<!-- <uni-section @click="goTo('/pages/jiangpin/index')" class="mb-10" title="奖品管理">
				<template v-slot:right>
					<uni-icons type="right" size="14"></uni-icons>
				</template>
			</uni-section> -->

			<uni-popup ref="popup" type="bottom" backgroundColor='#fff' border-radius="10px 10px 0 0">
				<scroll-view style="height: 100%;margin-bottom: 50px;" scroll-y="true">
					<uni-forms :modelValue="formData" ref="form" style="margin-top: 10px;">
						<uni-forms-item required label="积分" name="copyCount">

							<uni-easyinput type="text" v-model="formData.copyCount" placeholder="请输入积分" />
						</uni-forms-item>
					</uni-forms>
					<button type="primary" @click="submitForm">提交</button>
				</scroll-view>
			</uni-popup>

		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userName: "心淡如吟",
				count: 0,
				formData: {
					copyCount: 0,
				}
			}
		},
		onShow() {

			uni.getStorage({
				key: 'userName',
				success: (res) => {
					this.userName = res.data;
				},
				fail: function(err) {
					console.log(err)
				}
			});
			// uni.setStorage({
			// 	key: 'count',
			// 	data:9999,
			// });
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
		methods: {
			goTo(url) {
				uni.navigateTo({
						url: `${url}`
					},

				)
			},
			setCount() {
				this.formData.copyCount = JSON.parse(JSON.stringify(this.count))
				this.$refs.popup.open();

			},
			closeDrawer() {
				this.$refs.popup.close();
			},
			submitForm() {

				if (Number.isNaN(Number(this.formData.copyCount))) {
					uni.showToast({
						title: '请输入数字',
						icon: 'none'
					})
					return
				} else if (Number(this.formData.copyCount) < 0) {
					uni.showToast({
						title: '请输入正整数',
						icon: 'none'
					})
					return
				}
				this.count = Math.floor(Number(this.formData.copyCount))

				this.closeDrawer()
				uni.setStorage({
					key: 'count',
					data: this.count
				});

			}

		}
	}
</script>

<style lang="scss" scoped>
	.container {
		/* padding: 20px; */
		font-size: 14px;
		line-height: 24px;

		.user {
			/* padding: 20px; */
			margin-top: 20rpx;
			text-align: center;

			image {
				width: 120rpx;
				height: 120rpx;
			}
		}


	}
</style>