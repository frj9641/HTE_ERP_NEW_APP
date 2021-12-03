<template>
	<view>
		<text class="flex flex-direction form-title">新增表单</text>
		<view class="input-title">采样口</view>
		<picker mode="selector" @change="bindPortChange" :range="ports" :range-key="'collectPoint'">
			<input class="input-content" name="port" v-model="selectedPort" />
		</picker>
		<view class="input-title">指标</view>
		<picker mode="selector" @change="bindTestIndexChange" :range="testIndex" :range-key="'testIndex'">
			<input class="input-content" name="testIndex" v-model="selectedTestIndex" />
		</picker>

	</view>
</template>

<script>
	export default {
		name: 'detailInput',
		behaviors: ['uni://form-field'],
		created() {
			this.loadData()
		},
		data() {
			return {
				ports: [],
				testIndex: [],
				selectedPort: "",
				selectedTestIndex: "",
				detail: [],
				testIndexOptional: {}
			}
		},
		methods: {
			init(e) {
				this.detail = e
				console.log(this.detail)
			},
			loadData() {

				this.$http.get('/port/hteCollectPoint/list').then(res => {
					this.ports = res.data.result.records
					console.log(this.ports)
				})

				this.$http.get('/testIndex/hteTestIndex/list?pageSize=999').then(res => {
					this.testIndex = res.data.result.records
					console.log(this.testIndex)

				})

			},
			bindPortChange(e) {
				let obj = this.ports[e.detail.value];
				this.selectedPort = obj.collectPoint
				this.$emit('bindPortChange', obj.id)
			},
			bindTestIndexChange(e) {
				let obj = this.testIndex[e.detail.value];
				this.selectedTestIndex = obj.testIndex
				this.$emit('bindTestIndexChange', obj.id)
				this.$http.get('/algorithm/htePortDataCollectAlgorithm/queryByTestIndexId?testIndexId=' + obj.id).then(
					res => {
						this.testIndexOptional = res.data.result
						console.log(this.testIndexOptional)
					})
			}
		}
	}
</script>

<style>
	.form-title {
		background-color: #CCE6FF;
		font-size: 50rpx;
		text-align: center;
		padding: 20rpx;
	}

	.input-title {
		width: 100%;
		font-size: 40rpx;
		background-color: #F1F1F1;
		padding: 15rpx;
	}

	.input-content {
		margin: 15rpx;
	}
</style>
