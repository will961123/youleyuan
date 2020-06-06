<template>
	<view class="goodsListView px20">
		<view class="hotgoods bg-white radius">
			<view class="hot_tit">
				<image style="background-color: transparent;" class="bgimg" src="/static/hotgoods.png" mode=""></image>
				分类展示
			</view>
			<view v-if="list.length>0" class="listbox  flex justify-between flex-wrap">
				<view @click="gotoDetail(item.uuid)" class="item" v-for="(item, index) in list" :key="index">
					<image :src="item.homepage" style="border-radius: 14rpx; " mode="aspectFill"></image>
					<view class="title textov2">{{ item.title }}</view>
					<view class="moneybox flex justify-between align-center text-red"><!-- <view class="text-gray textov1   ">{{ item.text }}</view> --></view>
				</view>
			</view>
			<will-nodata v-else tittle="暂无数据"></will-nodata>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			categoryId: '',
			list: []
		};
	},
	onLoad(options) {
		options.categoryId ? (this.categoryId = options.categoryId) : '';
		this.categoryId && this.getList();
	},
	onShareAppMessage() {
		return {
			title: '分类',
			path: '/pages/category/goodsList?searchUserId=' + this.getUserId() + '&categoryId=' + this.categoryId
		};
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
				url: '/appCommodity/getcomlist',
				data: {
					menuid: this.categoryId
				},
				success: res => {
					uni.hideLoading();
					console.log('list', res);
					if (res.data.returnCode === 1) {
						res.data.list = res.data.list.map(i => {
							i.homepage = this.imgUrl + i.homepage;
							return i;
						});
						this.list = res.data.list;
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.goodsListView {
	padding-bottom: 40rpx;
	.hotgoods {
		padding: 0 30rpx;
		padding-top: 15px;
		min-height: 100vh;
		.hot_tit {
			margin: 0 auto;
			text-align: center;
			width: 220px;
			height: 30px;
			line-height: 30px;
			font-size: 36rpx;
			font-weight: bold;
			color: #333;
			position: relative;
			.bgimg {
				width: 100%;
				height: 100%;
				position: absolute;
				left: 0;
				top: 0;
			}
		}
		.listbox {
			padding-top: 30rpx;
			.item {
				width: 300rpx;
				margin-bottom: 20px;
				& > image {
					width: 300rpx;
					height: 300rpx;
					border-radius: 16rpx;
				}
				.title {
					font-size: 28rpx;
					color: #000;
					font-weight: 700;
					line-height: 1.8em;
				}
				.money {
					font-size: 28rpx;
					& > text {
						font-size: 18rpx;
					}
				}
			}
		}
	}
}
</style>
