<template>
	<view v-if="list.length > 0" class="listbox">
		<view @click="gotoDetail(item.cuuid)" v-for="(item, index) in list" :key="index" class="item bg-white flex radius">
			<image :src="item.homepage" mode="aspectFit"></image>
			<view class="infoBox flex flex-direction justify-between">
				<view class="title">{{ item.title }}</view>
				<view class="moneyBox flex  align-center justify-between ">
					<text class="text-gray textov1">{{ item.text }}</text>
					<!-- 					<text class="text-red">￥{{ item.price }}</text> -->
					<button @click.stop="del(item.cuuid)" class="btn cu-btn">取消收藏</button>
				</view>
			</view>
		</view>
	</view>

	<will-nodata v-else tittle="暂无收藏"></will-nodata>
</template>

<script>
export default {
	data() {
		return {
			list: []
		};
	},
	onLoad() {
		this.checkLogin().then(
			success => {
				this.getList();
			},
			file => {
				uni.showModal({
					title: '请先登录!',
					content: '请登录获取更好的体验!',
					showCancel: false,
					success: res => {
						if (res.confirm) {
							uni.switchTab({
								url: '/pages/my/my'
							});
						}
					}
				});
			}
		);
	},
	methods: {
		gotoDetail(id) {
			uni.navigateTo({
				url: '/pages/index/goodsDetail?goodsType=1&goodsId=' + id
			});
		},
		getList() {
			this.showLoading();
			this.request({
				url: '/appCollect/getColandCom',
				data: {
					clientopenid: this.getUserId()
				},
				success: res => {
					uni.hideLoading();
					console.log('收藏', res);
					if (res.data.returnCode === 1) {
						res.data.list = res.data.list.map(i => {
							i.homepage = this.imgUrl + i.homepage;
							return i;
						});
						this.list = res.data.list;
					} else {
						this.list = [];
					}
				}
			});
		},
		del(id) {
			uni.showModal({
				title: '取消收藏',
				content: '确定要取消收藏吗?',
				success: res => {
					if (res.confirm) {
						this.showLoading();
						let url = '/appCollect/deleteCollect';
						this.request({
							url,
							data: {
								commuuid: id,
								clientopenid: this.getUserId()
							},
							success: res => {
								uni.hideLoading();
								console.log('unlike', res);
								this.getList();
								// if (res.data.returnCode === 1) {
								// 	this.$set(this.goodsInfo, 'like', !this.goodsInfo.like);
								// }
							}
						});
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
page {
	padding-bottom: 40rpx;
}
.listbox {
	padding: 0 20rpx;
	.item {
		margin-bottom: 20rpx;
		padding: 26rpx 20rpx;
		& > image {
			width: 160rpx;
			height: 160rpx;
			margin-right: 22rpx;
		}
	}
	.infoBox {
		flex: 1;
		.title {
			font-weight: 700;
			font-size: 28rpx;
		}
		.moneyBox {
			font-size: 26rpx;
			.btn {
				padding: 0 16rpx;
				font-size: 22rpx;
				height: 46rpx;
				color: #fff;
				background-image: linear-gradient(-90deg, #ff585f 0%, #ff826a 100%);
			}
		}
	}
}
</style>
