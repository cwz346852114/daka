<template>
	<view style="margin: 40rpx;">
		<view style="display: flex;justify-content: center;margin-bottom: 40rpx">
			<uni-grid :column="3" :highlight="true">
				<uni-grid-item v-for="(item, index) in jiangpinList" :index="index" :key="index">
					<view :class="['grid-item-box',item.isShow?'active':'']">
						<view>{{index+1}}</view>
						<view class="text">{{ item.name }}</view>
					</view>
				</uni-grid-item>
			</uni-grid>
		</view>
		<view style="text-align: center;">
			<view style="margin-bottom: 40rpx;">积分：<text style="color: aqua;">{{count}}</text></view>
			<uni-button @click="choujiang" type='primary'>抽奖（200积分）</uni-button>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				count: 0,
				jiangpinList: [],
				myJiang: [],
				messageList: []
			}
		},
		onLoad() {
			this.init()
		},
		methods: {
			getChou() {



				let item = {}
				let random = Math.floor(Math.random() * 9)
				this.jiangpinList.forEach((el, index) => {
					el.isShow = false;
					if (index === random) {
						el.isShow = true;
						item = el
					}
				})

				uni.showToast({
					title: `恭喜您获得奖品${item.name}`,
					icon: "none"
				})
				// 加入到中奖
				
				if (this.myJiang.length > 0 && this.myJiang.find(el => el.code == item.code)) {
					this.myJiang.forEach(el => {
						if (el.code == item.code) {
							el.num += 1;
						}
					})
				} else {
					this.myJiang.push({
						name: item.name,
						num: 1, //数量
						code: item.code,
					})
				}
				// 使用积分
				this.count = this.count - 200;
				// 记录到消息				
				this.messageList.unshift({
					title: '抽奖中奖',
					content: `尊敬的用户您好，您已抽中商品${item.name}`,
					type: '中奖',
					time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`
				})
				uni.setStorage({
					key: 'messageList',
					data: this.messageList,
				})
				uni.setStorage({
					key: 'myJiang',
					data: this.myJiang,
				})
				uni.setStorage({
					key: 'count',
					data: this.count
				})
			},
			choujiang() {
				if (this.count > 200) {
					this.getChou();

				} else {
					uni.showToast({
						title: "积分不足",
						icon: "none"
					})
				}
			},
			init() {
				uni.getStorage({
					key: 'count',
					success: (res) => {
						this.count = res.data
					}
				})
				uni.getStorage({
					key: 'jiangpinList',
					success: (res) => {
						this.jiangpinList = res.data.map(el => {
							el.isShow = false;
							return el;
						})
					}
				})
				uni.getStorage({
					key: 'messageList',
					success: (res) => {
						this.messageList = res.data
					}
				})
				uni.getStorage({
					key: 'myJiang',
					success: (res) => {
						this.myJiang = res.data
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.grid-item-box {
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}

	.active {
		background: blue;
		color: #fff;
	}
</style>