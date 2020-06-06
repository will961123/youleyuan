<template>
	<view class="indexView px20">
		<view class="search bg-white flex align-center">
			<view class="searchiptbox flex align-center">
				<view style="width: 100%;" class="flex1 flex align-center justify-between">
					<input style="flex: 1;" v-model="searchTit" type="text" placeholder="请输入关键字" value="" />
					<text @click="getGoodsList" style="padding:  15rpx;" class="cuIcon cuIcon-search"></text>
				</view>
			</view>
		</view>
		<view class="hotgoods bg-white radius">
			<!-- 	<view class="hot_tit">
				<image style="background-color: transparent;" class="bgimg" src="/static/hotgoods.png" mode=""></image>
				商品展示
			</view> -->
			<view v-if="hootGoods.length > 0" class="listbox  flex justify-between flex-wrap">
				<view @click="gotoDetail(item.uuid)" class="item" v-for="(item, index) in hootGoods" :key="index">
					<image :src="item.homepage" style="border-radius: 14rpx; " mode="aspectFill"></image>
					<view class="title textov2">{{ item.title }}</view>
					<view class="moneybox flex justify-between align-center text-red">
						<!-- <view class="text-gray textov1   ">{{ item.text }}</view> -->
						<!-- <view class="money flex align-center">
							<text>￥</text>
							{{ item.price }}
						</view> -->
					</view>
				</view>
			</view>
			<will-nodata v-else tittle="请搜索商品"></will-nodata>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			hootGoods: [],
			searchTit: ''
		};
	},
	onLoad() {},
	onShareAppMessage() {
		return {
			title: '搜索',
			path: '/pages/index/search?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		getGoodsList() {
			if (!this.searchTit) {
				this.showToast('请输入关键字');
				return false;
			}
			this.hootGoods = [];
			this.showLoading();
			this.request({
				url: '/appCommodity/byLike',
				data: {
					title: this.searchTit
				},
				success: res => {
					uni.hideLoading();
					console.log('goods', res);
					if (res.data.returnCode === 1) {
						res.data.list = res.data.list.map(i => {
							i.homepage = this.imgUrl + i.homepage;
							return i;
						});
						this.hootGoods = res.data.list;
					} else if (res.data.returnCode === 2) {
						uni.showModal({
							title: '暂无商品',
							content: '没有查询到符合条件的商品!',
							showCancel: false
						});
					}
				}
			});
		},
		gotoDetail(id) {
			uni.navigateTo({
				url: '/pages/index/goodsDetail?goodsType=1&goodsId=' + id
			});
		}
	}
};
</script>

<style lang="scss">
.indexView {
	padding-bottom: 40rpx;
	.search {
		padding: 20rpx 30rpx;
		& > navigator {
			& > image {
				width: 20px;
				height: 20px;
				margin-right: 15px;
			}
		}
		.searchiptbox {
			background: #f5f5f5;
			height: 30px;
			border-radius: 15px;
			padding: 0 30rpx;
			font-size: 26rpx;
			flex: 1;

			& > image {
				width: 16px;
				height: 16px;
				margin-right: 20rpx;
			}
			input {
				height: 30px;
				flex: 1;
			}
		}
	}
	.hotgoods {
		min-height: 90vh;
		padding: 0 30rpx;
		padding-top: 15px;
		margin-top: 10px;
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
