<template>
	<view>
		<text class="flex flex-direction form-title">新增表单</text>
		<view class="input-title">厂站</view>
		<picker mode="selector" @change="bindSiteChange" :range="sites" :range-key="'siteName'">
			<input class="input-content" name="site" v-model="selectedSite" disabled="true" />
		</picker>

		<view class="input-title">采集日期</view>
		<picker mode="date" @change="bindCgDateChange">
			<input class="input-content" name="cgDate" v-model="selectedCgDate" disabled="true" />
		</picker>

		<view class="input-title">采集时间</view>
		<picker mode="time" @change="bindCgTimeChange">
			<input class="input-content" name="cgTime" v-model="selectedCgTime" disabled="true" />
		</picker>
	</view>
</template>

<script>
	export default {
		name: 'compInput',
		behaviors: ['uni://form-field'],
		created() {
			this.loadData()
		},
		data() {
			return {
				sites: [],
				selectedCgTime: "",
				selectedCgDate: "",
				selectedSite: ""
			}
		},
		methods: {
			loadData() {
				this.sites = getApp().globalData.userPermission.depart2work
			},
			bindCgDateChange(e) {
				this.selectedCgDate = e.detail.value
			},
			bindCgTimeChange(e) {
				this.selectedCgTime = e.detail.value
			},
			bindSiteChange(e) {
				let obj = this.sites[e.detail.value];
				this.selectedSite = obj.siteName
				this.$emit('bindSiteChange', obj.siteCode)
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
