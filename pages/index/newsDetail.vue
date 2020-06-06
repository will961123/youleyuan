<template>
	<view v-if="newsDetail.title">
		<view class="tit text-center bg-white">{{ newsDetail.title }}</view>
		<view style="padding:  0 25rpx;">
			<rich-text :nodes="newsDetail.text"></rich-text>
		</view>
		
	</view>
</template>

<script>
export default {
	data() {
		return {
			newsId: '',
			newsDetail: {}
		};
	},
	onLoad(options) {
		options.newsId ? (this.newsId = options.newsId) : '';
		this.newsId && this.getNewsDetail();
	},
	onShareAppMessage() {
		return {
			title: '资讯详情',
			path: '/pages/index/newsDetail?searchUserId=' + this.getUserId() + '&newsId=' + this.newsId
		};
	},
	methods: {
		getNewsDetail() {
			this.showLoading();
			this.request({
				url: '/appInformation/InformationByid',
				data: {
					uuid: this.newsId
				},
				success: res => {
					uni.hideLoading();
					console.log('newsDetail', res);
					if (res.data.returnCode === 1) {
						res.data.obj.text = res.data.obj.text.replace(/\<img/gi, "<img style='width:100%;height:auto;display:block'");
						this.newsDetail = res.data.obj;
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
