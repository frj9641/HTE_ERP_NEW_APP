<template>
	<view>
		<text class="flex flex-direction form-title">新增表单</text>
		<view class="input-title">厂站</view>
		<picker mode="selector" @change="bindSiteChange" :range="sites" :range-key="'siteName'">
			<input class="input-content" v-model="selectedSite" />
		</picker>

		<view class="input-title">物料</view>
		<picker mode="selector" @change="bindMaterialChange" :range="materials" :range-key="'material'">
			<input class="input-content" v-model="selectedMaterial" />
		</picker>

		<view class="input-title">供应商</view>
		<picker mode="selector" @change="bindSupplyChange" :range="supplys" :range-key="'supplyName'">
			<input class="input-content" v-model="selectedSupply" />
		</picker>

		<view class="input-title">数量(吨)</view>
		<input class="input-content" name="slT" type="digit" />

		<view class="input-title">采购单价(元/吨)</view>
		<input class="input-content" name="dj" type="digit" />

		<view class="input-title">运费单价(元/吨)</view>
		<input class="input-content" name="yfdj" type="digit" />

		<view class="input-title">备注</view>
		<input class="input-content" name="mark" type="text" />

		<view class="input-title">采购方式</view>
		<picker mode="selector" @change="bindCgWayChange" :range="cgWays" :range-key="'title'">
			<input class="input-content" name="cgWay" v-model="selectedCgWay" />
		</picker>

		<view class="input-title">采购日期</view>
		<picker mode="date" @change="bindCgDateChange">
			<input class="input-content" name="cgDate" v-model="selectedCgDate" />
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
				materials: [],
				supplys: [],
				cgWays: [],
				selectedMaterial: "",
				selectedSupply: "",
				selectedCgWay: "",
				selectedCgDate: "",
				selectedSite: ""
			}
		},
		methods: {
			loadData() {
				this.sites = getApp().globalData.userPermission.depart2work

				this.$http.get('/material/hteKcMaterial/rootList').then(res => {
					this.materials = res.data.result.records

				})

				this.$http.get('/supply/hteSupply/list').then(res => {
					this.supplys = res.data.result.records

				})

				this.$http.get('/sys/api/queryEnableDictItemsByCode?code=purchase_way').then(res => {
					this.cgWays = res.data
					console.log(this.cgWays)
				})
			},
			bindMaterialChange(e) {
				let obj = this.materials[e.detail.value];
				this.selectedMaterial = obj.material
				this.$emit('bindMaterialChange', obj.id)
			},
			bindSupplyChange(e) {
				let obj = this.supplys[e.detail.value];
				this.selectedSupply = obj.supplyName
				this.$emit('bindSupplyChange', obj.id)
			},
			bindCgWayChange(e) {
				let obj = this.cgWays[e.detail.value];
				this.selectedCgWay = obj.title
				this.$emit('bindCgWayChange', obj.value)
			},
			bindCgDateChange(e) {
				this.selectedCgDate = e.detail.value
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
