<template>
	<view class="productList">
		<view v-for="(item,index) in productList" :key="index">
			<view class="item">
				<view class="left">
					<image class="img" v-if="index%3==0" src="@/static/product/beiguo.png" alt="" />
					<image class="img" v-if="index%3==1" src="@/static/product/paigu.png" alt="" />
					<image class="img" v-if="index%3==2" src="@/static/product/jidan.png" alt="" />
				</view>
				<view class="center">
					<view>{{item.name}}</view>
					<view style="display: flex;">
						<view style="margin-right: 40rpx;">编号：{{item.code}}</view>
						<view>库存：{{item.number}}</view>
					</view>
					<view>价格：{{item.price}} 分</view>
					<view>类型：{{getTypeName(item.type)}}</view>
				</view>
				<view class="right">
					<view @click="editForm(item,index)" class="mb-10" style="color: cornflowerblue;">编辑</view>
					<view @click="remove(index)" style="color: cornflowerblue;">删除</view>
				</view>
			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="productList.length==0">暂无商品数据</view>
		<view class="addProduct">
			<button class="button" type="primary" @click="switchBtn()">添加商品</button>
		</view>
		
		<uni-popup ref="popup" type="bottom" backgroundColor='#fff' border-radius="10px 10px 0 0">
			<scroll-view style="height: 100%;" scroll-y="true">
				<uni-forms :modelValue="formData" ref="form" :rules="rules" style="margin-top: 10px;">
					<uni-forms-item label="商品名称" name="name">
						<uni-easyinput type="text" v-model="formData.name" placeholder="请输入商品名称" />
					</uni-forms-item>
					<uni-forms-item label="商品编号" name="code">
						<uni-easyinput type="text" v-model="formData.code" placeholder="请输入商品编号" />
					</uni-forms-item>
					<uni-forms-item label="商品类型" name="type">
						<picker @change="bindTypeChange" :value="typeIndex" :range="typeList" range-key="name">
							<view class="picker">
								{{formData.type ? getTypeName(formData.type) : '请选择商品类型'}}
							</view>
						</picker>
					</uni-forms-item>
					<uni-forms-item label="排序" name="sort">
						<uni-number-box v-model="formData.sort" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
					<uni-forms-item label="价格" name="price">
						<uni-number-box v-model="formData.price" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
					<uni-forms-item required label="库存" name="number">
						<uni-number-box v-model="formData.number" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
				</uni-forms>
				<button @click="submitForm">提交</button>
			</scroll-view>
		</uni-popup>

		<uni-popup ref="myDialog" type="dialog">
			<uni-popup-dialog title='是否确认删除该商品' :duration="2000" :before-close="true" @close="close"
				@confirm="confirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				typeIndex: -1,
				typeList: [],
				productList: [],
				title: '新增',
				formData: {
					name: '',
					code: '',
					type: '',
					price: "",
					number: 1,
					sort: 0
				},
				index: 0,
				rules: {
					name: {
						rules: [{
							required: true,
							errorMessage: '请填写商品名称',
						}],
					},
					code: {
						rules: [{
							required: true,
							errorMessage: '请填写商品编码',
						}],
					},
					type: {
						rules: [{
							required: true,
							errorMessage: '请选择商品类型',
						}],
					},
					price: {
						rules: [{
							required: true,
							errorMessage: '请填写商品价格',
						}],
					},
					number: {
						rules: [{
							required: true,
							errorMessage: '请填写库存数量',
						}],
					}
				}
			}
		},
		onLoad() {
			this.getList()
			this.getTypeList()
		},
		methods: {
			getTypeName(typeCode) {
				const type = this.typeList.find(item => item.code === typeCode)
				return type ? type.name : '未分类'
			},
			
			bindTypeChange(e) {
				this.typeIndex = e.detail.value
				this.formData.type = this.typeList[this.typeIndex].code
			},
			
			getTypeList() {
				uni.getStorage({
					key: 'typeList',
					success: (res) => {
						this.typeList = res.data || []
					},
					fail: () => {
						this.typeList = []
					}
				})
			},
			
			
			
			confirm() {
				const product = this.productList[this.index]
				
				// 从商品列表中删除
				this.productList.splice(this.index, 1)
				
			
				
				// 更新商品缓存
				uni.setStorage({
					key: 'productList',
					data: this.productList,
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
					key: 'productList',
					success: (res) => {
						this.productList = res.data || []
					},
					fail: () => {
						this.productList = []
					}
				})
			},
			
			switchBtn() {
				this.title = '新增'
				this.typeIndex = -1
				this.formData = {
					name: '',
					code: '',
					type: '',
					price: 1,
					number: 1,
					sort: this.productList.length + 1
				}
				this.$refs.popup.open()
			},
			
			closeDrawer() {
				this.$refs.popup.close()
			},
			
			editForm(item, index) {
				this.title = '编辑'
				this.formData = JSON.parse(JSON.stringify(item))
				this.index = index
				
				// 设置当前选中的typeIndex
				if (this.formData.type) {
					this.typeIndex = this.typeList.findIndex(t => t.code === this.formData.type)
				} else {
					this.typeIndex = -1
				}
				
				this.$refs.popup.open()
			},
			
			submitForm() {
				this.$refs.form.validate((err, formData) => {
					if (!err) {
						// 更新商品列表
						if (this.title == '新增') {
							this.productList.push(this.formData)
						} else {
							this.productList.splice(this.index, 1, this.formData)
						}
						
				
						// 排序商品列表
						this.productList.sort((x, y) => {
							if (!x.sort) x.sort = 0
							if (!y.sort) y.sort = 0
							return x.sort - y.sort
						})
						
						// 保存商品列表
						uni.setStorage({
							key: 'productList',
							data: this.productList,
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
				}
			}
			
			.center {
				flex: 1;
			}
			
			.right {
				display: flex;
				flex-direction: column;
			}
		}
	}
	
	.picker {
		padding: 12rpx 20rpx;
		border: 1rpx solid #e5e5e5;
		border-radius: 8rpx;
		color: #333;
		font-size: 28rpx;
	}
	
	.button {
		margin: 20rpx;
	}
</style>