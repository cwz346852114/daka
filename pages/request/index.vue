<template>
	<view class="productList">
		<view v-for="(item,index) in requestList">
			<view class="item">
				<view class="left">
					<image class="img" src="@/static/product/renwu.png" alt="" />
				
				</view>
				<view class="center">

					<view>{{item.name}}</view>
					<view>任务编号：{{item.code}}</view>
					<view>排序：{{item.sort}}</view>
					<view>
						<view>任务积分：{{item.price}} 分</view>
						<view>最高完成次数：{{item.number}} 分</view>
						<!-- <view>{{item.count}}</view> -->
					</view>

				</view>
				<view class="right">
					<view @click="editForm(item,index)" class="mb-10" style="color: cornflowerblue;">编辑</view>
					<view @click="remove(index)" style="color: cornflowerblue;">删除</view>
				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="requestList.length==0">暂无任务数据</view>
		<view class="addProduct">

			<button class="button" type="primary" @click="switchBtn()">添加任务 </button>

		</view>
		<uni-popup ref="popup" type="bottom" backgroundColor='#fff' border-radius="10px 10px 0 0">
			<scroll-view style="height: 100%;" scroll-y="true">
				<uni-forms :modelValue="formData" ref="form" :rules="rules" style="margin-top: 10px;">
					<uni-forms-item label="任务名称" name="name">
						<uni-easyinput type="text" v-model="formData.name" placeholder="请输入任务名称" />
					</uni-forms-item>
					<uni-forms-item label="任务编号" name="code">
						<uni-easyinput type="text" v-model="formData.code" placeholder="请输入任务编号" />
					</uni-forms-item>
					<uni-forms-item label="排序" name="sort">
						<uni-number-box v-model="formData.sort" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
					<uni-forms-item required label="最高完成次数" name="number">
						<uni-number-box v-model="formData.number" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
					<uni-forms-item label="任务积分" name="price">
						<uni-number-box v-model="formData.price" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>

				</uni-forms>
				<button @click="submitForm">提交</button>
			</scroll-view>
		</uni-popup>

		<uni-popup ref="myDialog" type="dialog">
			<uni-popup-dialog title='是否确认删除该任务' :duration="2000" :before-close="true" @close="close"
				@confirm="confirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				requestList: [],
				title: '新增',
				formData: {
					name: '',
					code: '',
					price: "",
					number: ""
				},
				index: 0,
				rules: {
					// 对name字段进行必填验证
					name: {
						// name 字段的校验规则
						rules: [
							// 校验 name 不能为空
							{
								required: true,
								errorMessage: '请填写任务名称',
							},
						],

					},
					code: {
						// name 字段的校验规则
						rules: [
							// 校验 name 不能为空
							{
								required: true,
								errorMessage: '请填写任务编码',
							},
						],

					},
					price: {
						// name 字段的校验规则
						rules: [
							// 校验 name 不能为空
							{
								required: true,
								errorMessage: '请填写任务积分',
							},
						],

					},
					number: {
						// name 字段的校验规则
						rules: [
							// 校验 name 不能为空
							{
								required: true,
								errorMessage: '请填写最高完成次数',
							},
						],

					}
				}

			}
		},
		onLoad() {
			this.getList()
		},
		methods: {
			confirm() {
				this.requestList.splice(this.index, 1)
				uni.setStorage({
					key: 'requestList',
					data: this.requestList,
					success: () => {
						this.$refs.myDialog.close()
						this.getList()

					}
				})
			},
			remove(index) {
				this.index = index
				this.$refs.myDialog.open()
			},
			close() {
				this.$refs.myDialog.close()
			},
			getList() {
				uni.getStorage({
					key: 'requestList',
					success: (res) => {
						this.requestList = res.data;
					},
					fail: function(err) {
						console.log(err)
					}
				});
			},
			switchBtn() {
				this.title = '新增'
				this.formData = {
					number:1,
					sort:this.requestList.length+1,
					price:1
				}
				this.$refs.popup.open();
			},
			closeDrawer() {
				this.$refs.popup.close();
			},
			editForm(item, index) {
				this.title = '编辑'
				this.formData = JSON.parse(JSON.stringify(item));
				this.index = index;
				this.$refs.popup.open();

			},
			submitForm() {
				this.$refs.form.validate((err, formData) => {
					// 如果校验成功 ，err 返回 null
					if (!err) {

						if (this.title == '新增') {
							this.requestList.push({
								...this.formData,
								count: 0
							})
						} else {
							this.requestList.splice(this.index, 1, this.formData)
						}
						this.requestList.sort((x, y) => {
							if (!x.sort) {
								x.sort = 0
							}
							if (!y.sort) {
								y.sort = 0
							}
							return x.sort - y.sort
						})
						uni.setStorage({
							key: 'requestList',
							data: this.requestList,
							success: () => {
								this.closeDrawer()
								this.getList()

							}
						})
					}
				})

			}
		}
	}
</script>

<style lang="scss" scoped>
	.productList {
		padding-bottom: 90rpx;

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