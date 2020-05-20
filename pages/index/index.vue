<template>
	<view class="indexView px20">
		<swiper class="screen-swiper square-dot radius" :indicator-dots="true" :autoplay="true">
			<swiper-item v-for="(item, index) in swiperList" class="radius" :key="index"><image :src="item.pic" mode="aspectFill"></image></swiper-item>
		</swiper>

		<view class="categoryList  bg-white radius flex">
			<view v-for="(item, index) in categoryList" :key="index" class="item text-center flex flex-direction align-center">
				<image :src="item.icon" mode="aspectFill"></image>
				<text>{{ item.name }}</text>
			</view>
		</view>

		<view class="hotgoods bg-white radius">
			<view class="hot_tit">
				<image style="background-color: transparent;" class="bgimg" src="/static/hotgoods.png" mode=""></image>
				精品展示
			</view>
			<view class="listbox  flex justify-between flex-wrap">
				<view @click="gotoDetail(item.uuid)" class="item" v-for="(item, index) in hootGoods" :key="index">
					<image :src="item.homepage" style="border-radius: 14rpx; " mode="aspectFill"></image>
					<view class="title textov2">{{ item.text }}</view>
					<view class="moneybox flex justify-between align-center text-red">
						<!-- <view class="money flex align-center">
							<text>￥</text>
							{{ item.price }}
						</view> -->
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			swiperList: [],
			categoryList: new Array(8).fill({ name: '分类名字', id: 'id1', icon: '' }),
			hootGoods: []
		};
	},
	onLoad() {
		this.getBannerList();
		this.getGoodsList();
	},
	methods: {
		getGoodsList() {
			this.request({
				url: '/appCommodity/getActive',
				data: {
					active: 1
				},
				success: res => {
					console.log('goods', res);
					if (res.data.returnCode === 1) {
						res.data.list = res.data.list.map(i => {
							i.homepage = this.imgUrl + i.homepage;
							return i;
						});
						this.hootGoods = res.data.list;
					}
				}
			});
		},
		getBannerList() {
			this.request({
				url: '/appBackground/getBackgroundlist',
				success: res => {
					console.log('banner', res);
					if (res.data.returnCode === 1) {
						res.data.list = res.data.list.map(i => {
							i.pic = this.imgUrl + i.pic;
							return i;
						});
						this.swiperList = res.data.list;
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
	padding-top: 16rpx;
	padding-bottom: 40rpx;
	.categoryList {
		flex-wrap: wrap;
		padding: 0 30rpx;
		padding-bottom: 26rpx;
		margin-top: 16rpx;
		.item {
			width: 25%;
			& > image {
				width: 100rpx;
				height: 100rpx;
				border-radius: 50%;
				margin: 30rpx 0;
			}
			& > text {
				color: #8e8e8e;
				font-size: 18rpx;
			}
		}
	}

	.hotgoods {
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
