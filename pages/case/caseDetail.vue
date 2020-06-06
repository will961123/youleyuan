<template>
	<view v-if="caseDetail.title">
		<view class="tit text-center bg-white">{{ caseDetail.title }}</view>
		<view style="padding:  0 25rpx;">
			<rich-text s :nodes="caseDetail.text"></rich-text>
		</view>
		
	</view>
</template>

<script>
export default {
	data() {
		return {
			caseId: '',
			caseDetail: {}
		};
	},
	onLoad(options) {
		options.caseId ? (this.caseId = options.caseId) : '';
		this.caseId && this.getCaseDetail();
	},
	onShareAppMessage() {
		return {
			title: '案例详情',
			path: '/pages/case/caseDetail?searchUserId=' + this.getUserId() + '&caseId=' + this.caseId
		};
	},
	methods: {
		getCaseDetail() {
			this.showLoading();
			this.request({
				url: '/appCase/getCaseByuuid',
				data: {
					uuid: this.caseId
				},
				success: res => {
					uni.hideLoading();
					console.log('caseDetail', res);
					if (res.data.returnCode === 1) {
						res.data.obj.text = res.data.obj.text.replace(/\<img/gi,"<img style='width:100%;height:auto;display:block'")
						this.caseDetail = res.data.obj;
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.tit {
	line-height: 40px;
	font-size: 26rpx;
	color: #000;
	font-weight: 700;
	margin-bottom: 8rpx;
}
</style>
