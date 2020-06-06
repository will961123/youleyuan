<template>
	<view class="fadeback bg-white"> 
		<textarea v-model="fadeback" placeholder="请输入反馈内容和联系方式" />
		<button @click="saveFadeback" class="btn cu-btn text-white">提交</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			fadeback: ''
		};
	},
	onLoad() {},
	onShareAppMessage() {
		return {
			title: '意见反馈',
			path: '/pages/my/feedBack?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		saveFadeback() {
			let phone = uni.getStorageSync('phone')
			if(!phone){
				uni.showModal({
					title: '登录',
					content: '请授权手机号',
					success: res => {
						if (res.confirm) {
							uni.switchTab({
								url: '/pages/my/my'
							});
						}
					}
				});
				return false;
			}
			if (!this.fadeback) {
				this.showToast('请输入反馈内容');
				return false;
			}
			 
			this.showLoading();
			this.request({
				url: '/appFeedback/saveFeedback',
				data: {
					wxopenid:this.getUserId(),
					text: this.fadeback,
					phone:phone
				},
				success: res => {
					console.log('fadeback',res)
					uni.hideLoading();
					if (res.data.returnCode === 1) {
						this.showToast('提交成功');
						this.fadeback =''
					} else {
						this.showToast(res.data.returnStr);
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
page {
	height: 100%;
}
.fadeback {
	min-height: 100%;
	padding: 30rpx;
	& > textarea {
		width: 100%;
		height: 360rpx;
		background-color: #f5f5f5;
		border-radius: 10rpx;
		padding: 28rpx 24rpx;
		font-size: 28rpx;
	}
	& > button {
		width: 100%;
		height: 88rpx;
		background-image: linear-gradient(-90deg, #ff585f 0%, #ff826a 100%);
		border-radius: 44rpx;
		font-size: 32rpx;
		letter-spacing: 1rpx;
		margin-top: 40rpx;
	}
}
</style>
