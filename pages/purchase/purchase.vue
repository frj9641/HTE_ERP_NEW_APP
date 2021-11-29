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

			<!-- <view class="padding flex flex-direction">
				<app-select label=" 类    型：" v-model="type" placeholder="请选择类型" :dict="plan_type" space></app-select>
			</view> -->

			<!-- <view class="padding flex flex-direction">
				<my-date label="开始时间：" v-model="beginTime" placeholder="请选择开始时间" required fields="minute"></my-date>
			</view> -->

			<!-- <view class="padding flex flex-direction">
				<uni-calendar :showMonth="true" :selected="selected" />
			</view>
			
			<view class="padding flex flex-direction">
				<my-image-upload />
			</view> -->

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
							<comp-input/>
							<button form-type="submit" class="submit-button">提交</button>
						</form>
					</view>
				</scroll-view>
			</uni-drawer>
		</view>

	</view>
</template>

<script>
	// const plan_type = [{
	// 	text: '日常记录',
	// 	value: '1'
	// }, {
	// 	text: '本周工作',
	// 	value: '2'
	// }, {
	// 	text: '下周计划',
	// 	value: '3'
	// }];
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
				list: []
				// item: {
				// 	msg: '退出成功'
				// },
				// plan_type,
				// type: "1",
				// selected: [],
				// beginTime: ''
			}
		},
		methods: {
			onSubmit(e) {
				console.log(e)
			},
			showDrawer() {
				this.$refs.showRight.open();
			},
			add() {
				this.showDrawer()
			},
			loadData() {
				this.$http.get('/purchase/hteKcMaterialPurchase/list').then(res => {
					this.list = res.data.result.records
					console.log(this.list)
				})
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
	
	.submit-button{
		background-color: #4CD964;
		color: #FFFFFF;
	}
</style>
