<template>
	<view>
		<uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="button"
			activeColor="#4cd964"></uni-segmented-control>
		<view class="item" :key="item.time" v-for="item in copyList">
			<view class="top">
				<view style="flex: 1;">{{item.title}}</view>
				<view><uni-tag type="success" size="mini" :text="item.type"></uni-tag></view>
			</view>
			<view class="center">
				{{item.content}}
			</view>
			<view class="bottom">
				{{item.time}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				messageList: [],
				copyList: [],
				items: ['全部', '购买', '使用', '退款', '中奖'],
				current: 0
			}
		},
		onShow() {
			uni.getStorage({
				key: 'messageList',
				success: (res) => {
					this.messageList = res.data;
					this.copyList = JSON.parse(JSON.stringify(this.messageList))
				},
				fail: function(err) {
					console.log(err, 222)
				}
			});
		},
		methods: {
			onClickItem(e) {
		
				if (this.current != e.currentIndex) {
					this.current = e.currentIndex;
				}
				console.log(e.currentIndex)
				switch (e.currentIndex) {
					case 0:
						this.copyList = this.messageList;
						break;
					case 1:
						this.copyList = this.messageList.filter(item => item.type == '购买');
						break;
					case 2:
						this.copyList = this.messageList.filter(item => item.type == '使用');
						break;
					case 3:
						this.copyList = this.messageList.filter(item => item.type == '退款');
						break;
					case 4:
						this.copyList = this.messageList.filter(item => item.type == '中奖');
						break;
				}
			}
		}
	}
</script>

<style lang="scss">
	.item {
		margin: 20rpx;
		padding: 20rpx;
		background-color: #fff;

		.top {
			display: flex;
			font-size: 18px;
			font-weight: 600;
			margin-bottom: 10rpx;
		}

		.center {
			margin-bottom: 10rpx;
		}

		.bottom {
			color: #999;
			margin-bottom: 10rpx;
		}
	}
</style>