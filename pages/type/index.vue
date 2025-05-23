<template>
	<view class="productList">
		<view v-for="(item,index) in typeList">
			<view class="item">
				
				<view class="center">

					<view>{{item.name}}</view>
					<view style="display: flex;">
						<view style="margin-right: 40rpx;">编号：{{item.code}}</view>
			
					</view>
				

				</view>
				<view class="right">
					<view @click="editForm(item,index)" class="mb-10" style="color: cornflowerblue;">编辑</view>
					<view @click="remove(index)" style="color: cornflowerblue;">删除</view>
				</view>

			</view>
		</view>
		<view style="text-align: center;margin-top: 50%;" v-if="typeList.length==0">暂无类型数据</view>
		<view class="addProduct">

			<button class="button" type="primary" @click="switchBtn()">添加类型</button>

		</view>
		<uni-popup ref="popup" type="bottom" backgroundColor='#fff' border-radius="10px 10px 0 0">
			<scroll-view style="height: 100%;" scroll-y="true">
				<uni-forms :modelValue="formData" ref="form" :rules="rules" style="margin-top: 10px;">
					<uni-forms-item label="类型名称" name="name">
						<uni-easyinput type="text" v-model="formData.name" placeholder="请输入商品名称" />
					</uni-forms-item>
					<uni-forms-item label="类型编号" name="code">
						<uni-easyinput   :disabled="title=='编辑'?true:false" v-model="formData.code" placeholder="请输入商品编号" />
					</uni-forms-item>
					<uni-forms-item label="排序" name="sort">
						<uni-number-box v-model="formData.sort" :min="0" :max="9999"></uni-number-box>
					</uni-forms-item>
					
				</uni-forms>
				<button @click="submitForm">提交</button>
			</scroll-view>
		</uni-popup>

		<uni-popup ref="myDialog" type="dialog">
			<uni-popup-dialog title='是否确认删除该类型' :duration="2000" :before-close="true" @close="close"
				@confirm="confirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				typeList: [],
				title: '新增',
				formData: {
					name: '',
					code: '',
					sort:"",
				
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
								errorMessage: '请填写类型名称',
							},
						],

					},
					code: {
						// name 字段的校验规则
						rules: [
							// 校验 name 不能为空
							{
								required: true,
								errorMessage: '请填写类型编码',
							},
						],

					},
				}

			}
		},
		onLoad() {
			this.getList()
		},
		methods: {
			confirm() {
				this.typeList.splice(this.index, 1)
				uni.setStorage({
					key: 'typeList',
					data: this.typeList,
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
					key: 'typeList',
					success: (res) => {
						this.typeList = res.data;
						console.log(this.typeList)
					},
					fail: function(err) {
						console.log(err)
					}
				});
			},
			switchBtn() {
				this.title = '新增'
				this.formData = {
					sort:this.typeList.length+1,
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
							
							this.typeList.push(this.formData)
						} else {
							this.typeList.splice(this.index, 1, this.formData)
						}
						
						this.typeList.sort((x,y)=>{
							if(!x.sort){
								x.sort = 0
							}
							if(!y.sort){
								y.sort = 0
							}
							return x.sort-y.sort
						})
					console.log(this.typeList)
						uni.setStorage({
							key: 'typeList',
							data: this.typeList,
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

			.right {}
		}
	}
</style>