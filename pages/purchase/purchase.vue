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
				<uni-collapse :style="{'background-color':item.checkFlag==1?'#FFA500':'#fff'}">
					<uni-collapse-item :showArrow="false" :title="item.djhDesc" :open="index==0?true:false">
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
							<button style="position: absolute; right: 0; bottom: 0;" v-if="item.checkFlag==1"
								size="mini" type="primary" @click="updateCheck(item.id)">审核通过</button>
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
								@bindCgWayChange="bindCgWayChange" @bindSiteChange="bindSiteChange" />
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
				selectedCgWayId: '',
				selectedSiteId: ''

			}
		},
		methods: {
			onSubmit(e) {
				let that = this
				let form = e.detail.value
				let params = {}
				params.siteId = this.selectedSiteId
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
				params.djhDesc = form.site + "-" + form.material + "-" + form.cgWay + "-" + form.cgDate //测试
				params.cgDate = form.cgDate
				this.$http.get('/purchase/hteKcMaterialPurchase/getCgNo?id=' + this.selectedSiteId).then(res => {
					params.djh = res.data.message
					that.$http.post("/purchase/hteKcMaterialPurchase/add", params).then(res => {
						console.log(res)
					})
				})
				this.$refs.showRight.close();
				this.loadData()
			},
			showDrawer() {
				this.$refs.showRight.open();
			},
			add() {
				this.showDrawer()
			},
			loadData() {
				this.$http.get('/purchase/hteKcMaterialPurchase/list?pageSize=50&creater=e9ca23d68d884d4ebb19d07889727dae')
					.then(res => {
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
			},
			bindSiteChange(e) {
				this.selectedSiteId = e
			},
			updateCheck(e) {
				let params = {}
				params.id = e
				params.checkFlag = 2
				params.checkDate = this.dateFormat(new Date())
				this.$http.put('/purchase/hteKcMaterialPurchase/edit', params)
					.then(res => {
						console.log(res)
					})
				this.loadData()
			},
			dateFormat(time) {
				let date = new Date(time);
				let year = date.getFullYear();
				// 在日期格式中，月份是从0开始的，因此要加0，使用三元表达式在小于10的前面加0，以达到格式统一  如 09:11:05
				let month = date.getMonth() + 1 < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1;
				let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
				let hours = date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
				let minutes = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
				let seconds = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
				// 拼接
				// return year + "-" + month + "-" + day + " " + hours + ":" + minutes + ":" + seconds;
				return year + "-" + month + "-" + day;
			},
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
