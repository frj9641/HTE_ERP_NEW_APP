<template>
	<view>
		<scroll-view style="position: absolute; z-index: 0;" :scroll-y="modalName==null" class="page"
			:class="modalName!=null?'show':''">
			<cu-custom id="nav-bar" bgColor="bg-gradual-green" :isBack="true">
				<block slot="content">质量数据采集</block>
				<!-- <block slot="right">
					<image class="image-add" src="./add.svg" @tap="add()"></image>
				</block> -->
			</cu-custom>

			<image class="image-add" src="/static/svg/add.svg" @tap="add()"></image>

			<view v-for="(item,index) in list" style="margin: 15rpx;">
				<uni-collapse
					:style="{'background-color':item.checkFlag==0?'#99FFFF':item.checkFlag==1?'#FFA500':'#fff'}">
					<uni-collapse-item :showArrow="false"
						:title="item.siteId_dictText+'-'+item.collectDate+'-'+item.collectTime">
						<view class="collapse-item" @click.stop="edit(item)">
							<view>厂站：{{item.siteId_dictText}}</view>
							<view>采样时间：{{item.collectTime}}</view>
							<view>采样日期：{{item.collectDate}}</view>
							<view>审核状态：{{item.checkFlag_dictText}}</view>
							<view>审核日期：{{item.checkDate}}</view>
							<view style="font-size: large;">======已完成项======</view>
							<view v-for="(d) in item.detail">
								<view>{{d.collectPointName}}-{{d.testIndexName}}-{{d.testValue}}</view>
							</view>

							<button style="position: absolute; right: 0; bottom: 0;" v-if="item.checkFlag==0"
								size="mini" type="primary"
								@click.stop="updateCheck(item.id,item.checkFlag)">提交审核</button>
							<button style="position: absolute; right: 0; bottom: 0;" v-if="item.checkFlag==1"
								size="mini" type="primary"
								@click.stop="updateCheck(item.id,item.checkFlag)">审核通过</button>
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
							<comp-input @bindSiteChange="bindSiteChange" />
							<button form-type="submit" class="submit-button-complete">提交</button>
						</form>
					</view>
				</scroll-view>
			</uni-drawer>
		</view>

		<view style="position: absolute; z-index: 1;">
			<uni-drawer ref="showDetail" mode="left" :mask-click="true">
				<scroll-view style="height: 100%;" scroll-y="true">
					<view class="content">
						<form @submit="onDetailSubmit">
							<detailInput @bindTestIndexChange="bindTestIndexChange" @bindPortChange="bindPortChange"
								ref="detailForm" />
							<button form-type="submit" class="submit-button-complete">完成并新建</button>
						</form>
						<button @click="closeDrawer()" class="submit-button-complete-next">退出</button>
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
	import detailInput from './detailInput.vue'

	export default {
		components: {
			appSelect,
			myImageUpload,
			myDate,
			compInput,
			detailInput
		},
		created() {
			this.loadData()
		},
		data() {
			return {
				modalName: null,
				list: [],
				selectedSiteId: '',
				selectedPortId: '',
				selectedTestIndexId: '',
				selectedMain: {}
			}
		},
		methods: {
			closeDrawer() {
				this.$refs.showDetail.close();
			},
			edit(e) {
				this.$refs.showDetail.open();
				this.$nextTick(function() {
					this.$refs.detailForm.init(e.detail);
				})
				this.selectedMain = e
			},
			// 后台接口不发生更改的最简易写法，但是前台代码发生了循环调用接口，并且重复强制刷新，如果实际使用此处务必修改
			loadData() {
				var that = this
				this.$http.get('/dataCollect/htePortDataCollect/list')
					.then(res => {
						this.list = res.data.result.records
						this.list.forEach(function(item) {
							that.loadDetail(item)
						});
					})

			},
			loadDetail(e) {
				let id = e.id
				this.$http.get('/dataCollect/htePortDataCollect/queryHtePortDataCollectDetailByMainId?id=' + id)
					.then(res => {
						e.detail = res.data.result
						this.$forceUpdate()
					})
			},
			onDetailSubmit(e) {
				let form = e.detail.value
				let keys = Object.keys(form)
				let detail = ''
				keys.forEach((key) => {
					detail = detail + key + ':' + form[key] + ';'
				})
				let params = {}
				params.parentId = this.selectedMain.id
				params.collectPointId = this.selectedPortId
				params.testIndexId = this.selectedTestIndexId
				params.detailValue = detail
				this.$http.post("/dataCollect/htePortDataCollect/addDetail", params).then(res => {
					this.$http.get('/dataCollect/htePortDataCollect/queryHtePortDataCollectDetailByMainId?id=' +
							this.selectedMain.id)
						.then(res => {
							this.$nextTick(function() {
								this.$refs.detailForm.init(res.data.result)
							})
						})
				})
				// 刷新外面，上方单独请求了一次抽屉表单的内容，属于重复请求
				this.loadData()
				uni.showToast({
					title: '已完成项更新',
					duration: 2000
				})
			},
			onSubmit(e) {
				let form = e.detail.value
				let params = {}
				params.siteId = this.selectedSiteId
				params.collectDate = form.cgDate
				params.collectTime = form.cgTime
				params.creater = getApp().globalData.userPermission.userId
				params.checkFlag = 0 //编辑中
				this.$http.post("/dataCollect/htePortDataCollect/add", params).then(res => {
					console.log(res)
					this.$refs.showRight.close();
					this.loadData(this.selectedMain)
				})
			},
			showDrawer() {
				this.$refs.showRight.open();
			},
			add() {
				this.showDrawer()
			},
			bindSiteChange(e) {
				this.selectedSiteId = e
			},
			bindPortChange(e) {
				this.selectedPortId = e
			},
			bindTestIndexChange(e) {
				this.selectedTestIndexId = e
			},
			updateCheck(e, c) {
				var that = this
				let params = {}
				params.id = e
				params.checkFlag = c == 0 ? 1 : 2
				params.checkDate = this.dateFormat(new Date())
				// 审核成功会删除附表记录
				this.$http.put('/dataCollect/htePortDataCollect/check', params)
					.then(res => {
						console.log(res)
						that.loadData()
					})

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
		/* width: 80rpx;
		height: 80rpx; */

		width: 120rpx;
		height: 120rpx;
		margin: 10rpx;
		position: fixed;
		right: 0;
		bottom: 0;
	}

	.submit-button-complete {
		margin-top: 100rpx;
		background-color: #4CD964;
		color: #FFFFFF;
	}

	.submit-button-complete-next {
		margin-top: 5rpx;
		background-color: #E43D33;
		color: #FFFFFF;
	}
</style>
