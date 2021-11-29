<template>
	<view>
		<text class="flex flex-direction form-title">新增表单</text>
		<view class="input-title">厂站</view>
		<input name="site" type="text" :value="value" />
		<view class="input-title">物料</view>
		<input name="material" type="text" :value="value" />
		<view class="input-title">供应商</view>
		<input name="supply" type="text" :value="value" />
		<view class="input-title">数量(吨)</view>
		<input name="slT" type="text" :value="value" />
		<view class="input-title">采购单价</view>
		<input name="dj" type="text" :value="value" />
		<view class="input-title">运费单价</view>
		<input name="yfdj" type="text" :value="value" />
		<view class="input-title">备注</view>
		<input name="mark" type="text" :value="value" />
		<view class="input-title">采购方式</view>
		<input name="cg_way" type="text" :value="value" />
		<view class="input-title">采购日期</view>
		<input name="cg_date" type="text" :value="value" />
	</view>
</template>

<script>
	export default {
		name: 'compInput',
		behaviors: ['uni://form-field'],
		created() {
			loadData()
		},
		methods: {
			loadData() {
				this.$http.get('').then(res => {
					console.log(res)
				})
			}
		}
	}
</script>

<style>
	.form-title {
		font-size: 50rpx;
		text-align: center;
		padding: 25rpx;
	}

	.input-title {
		width: 100%;
		font-size: 40rpx;
		background-color: #F1F1F1;
		padding: 5rpx;
	}
</style>
