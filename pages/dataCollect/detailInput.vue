<template>
	<view>
		<text class="flex flex-direction form-title">新增表单</text>

		<view>
			<view class="input-title">已完成项</view>
			<view v-for="item in detail">
				<input class="input-content" disabled="true"
					:value="item.collectPointName+' - '+item.testIndexName+' - '+item.testValue" />
			</view>
		</view>

		<view class="input-title">采样口</view>
		<picker mode="selector" @change="bindPortChange" :range="ports" :range-key="'collectPoint'">
			<input class="input-content" v-model="selectedPort" />
		</picker>
		<view class="input-title">指标</view>
		<picker mode="selector" @change="bindTestIndexChange" :range="testIndex" :range-key="'testIndex'">
			<input class="input-content" v-model="selectedTestIndex" />
		</picker>

		<view v-if="isShow">
			<view class="input-title">{{selectedTestIndex}}表达式</view>
			<input class="input-content" disabled="true" :value="polandExp" />
			<view v-for="item in optional">
				<view class="input-title">{{item}}</view>
				<input class="input-content" :name="item" type="digit" />
			</view>
		</view>

		<view v-if="!isShow">
			<view class="input-title">计算值</view>
			<input class="input-content" name="a" type="digit" />
		</view>

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
				optional: [],
				polandExp: '',
				isShow: false
			}
		},
		methods: {
			init(e) {
				this.detail = e
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
						let result = res.data.result
						if (result != null && result.id != null) {
							this.isShow = true
							this.optional = result.optional
							this.polandExp = result.polandExp
						} else {
							this.isShow = false
						}
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
