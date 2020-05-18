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
	methods: {
		saveFadeback() {
			if (!this.fadeback) {
				this.showToast('请输入反馈内容');
				return;
			}
			return
			this.showLoading();
			this.request({
				url: '/',
				data: {
					fadeback: this.fadeback
				},
				success: res => {
					uni.hideLoading();
					if (res.data.returnCode === 1) {
						this.showToast('提交成功');
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
