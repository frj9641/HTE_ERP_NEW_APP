<template>
	<view>
		<scroll-view style="position: absolute; z-index: 0;" :scroll-y="modalName==null" class="page"
			:class="modalName!=null?'show':''">
			<cu-custom id="nav-bar" bgColor="bg-gradual-green" :isBack="true">
				<block slot="content">采购业务</block>
				<block slot="right">
					<image class="image-add" src="./add.svg" @tap="add()"></image>
				</block>
			</cu-custom>

			<view v-for="(item,index) in list" style="margin: 15rpx;">
				<uni-collapse>
					<uni-collapse-item
						:thumb="item.checkFlag==2?'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png':''"
						:title="item.djhDesc" :open="index==0?true:false">
						<view class="collapse-item">
							<view>单据号：{{item.djh}}</view>
							<view>厂站：{{item.siteId_dictText}}</view>
							<view>物料：{{item.materialId_dictText}}</view>
							<view>供应商：{{item.supplyId_dictText}}</view>
							<view>单价：{{item.dj}} 元/吨</view>
							<view>运费：{{item.yfdj}} 元/吨</view>
							<view>数量：{{item.slT}} 吨</view>
							<view>总金额：{{item.zje}} 元</view>
							<view>采购人：{{item.creater}}</view>
							<view>采购方式：{{item.purchaseWay_dictText}}</view>
							<view>采购日期：{{item.cgDate}}</view>
							<view>备注：{{item.remark}}</view>
							<view>审核状态：{{item.checkFlag_dictText}}</view>
							<view>审核日期：{{item.checkDate}}</view>
							<view>入库状态：{{item.rkFlag_dictText}}</view>
						</view>
					</uni-collapse-item>
				</uni-collapse>
			</view>

		</scroll-view>

		<view style="position: absolute; z-index: 1;">
			<uni-drawer ref="showRight" mode="left" :mask-click="true">
				<scroll-view style="height: 100%;" scroll-y="true">
					<view class="content">
						<form @submit="onSubmit">
							<comp-input @bindMaterialChange="bindMaterialChange" @bindSupplyChange="bindSupplyChange"
								@bindCgWayChange="bindCgWayChange" />
							<button form-type="submit" class="submit-button">提交</button>
						</form>
					</view>
				</scroll-view>
			</uni-drawer>
		</view>

	</view>
</template>

<script>
	import appSelect from '@/components/my-componets/appSelect.vue'
	import myImageUpload from '@/components/my-componets/my-image-upload.vue'
	import myDate from '@/components/my-componets/my-date.vue'
	import compInput from './comInput.vue'

	export default {
		components: {
			appSelect,
			myImageUpload,
			myDate,
			compInput
		},
		created() {
			//
			this.loadData()
		},
		data() {
			return {
				modalName: null,
				list: [],
				selectedMaterialId: '',
				selectedSupplyId: '',
				selectedCgWayId: ''

			}
		},
		methods: {
			onSubmit(e) {
				let that = this
				let form = e.detail.value
				let params = {}

				params.siteId = 1 //权限未做暂时写死
				params.materialId = this.selectedMaterialId
				params.supplyId = this.selectedSupplyId
				params.slT = form.slT
				params.dj = form.dj
				params.yfdj = form.yfdj
				params.zje = (parseFloat(form.dj) + parseFloat(form.yfdj)) * parseFloat(form.slT)
				params.creater = getApp().globalData.userPermission.userId
				params.remark = form.mark
				params.checkFlag = 1 //未审核
				params.rkFlag = 0 //未入库
				params.purchaseWay = this.selectedCgWayId
				params.djhDesc = 'test' //测试
				params.cgDate = form.cgDate
				this.$http.get('/purchase/hteKcMaterialPurchase/getCgNo?id=' + 1).then(res => {
					params.djh = res.data.message
					that.$http.post("/purchase/hteKcMaterialPurchase/add", params).then(res => {
						console.log(res)
					})
				})
			},
			showDrawer() {
				this.$refs.showRight.open();
			},
			add() {
				this.showDrawer()
			},
			loadData() {
				this.$http.get('/purchase/hteKcMaterialPurchase/list?pageSize=50').then(res => {
					this.list = res.data.result.records
					console.log(this.list)
				})
			},
			bindMaterialChange(e) {
				this.selectedMaterialId = e
			},
			bindSupplyChange(e) {
				this.selectedSupplyId = e
			},
			bindCgWayChange(e) {
				this.selectedCgWayId = e
			}
		}
	}
</script>

<style>
	.collapse-item {
		font-size: 20rpx;
		margin-left: 50rpx;
		color: #999999;
	}

	.image-add {
		width: 80rpx;
		height: 80rpx;
	}

	.submit-button {
		background-color: #4CD964;
		color: #FFFFFF;
	}
</style>
