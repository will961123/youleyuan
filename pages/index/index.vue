<template>
	<view class="indexView px20">
		<view class="search bg-white flex align-center">
			<view class="searchiptbox flex align-center">
				<navigator style="width: 100%;" hover-class="none" class="flex1 flex align-center justify-between" url="/pages/index/search">
					<input style="flex: 1;" disabled="true" type="text" placeholder="请输入关键字" value="" /> 
					<text class="cuIcon cuIcon-search"></text>
				</navigator>
			</view>
		</view>
		<swiper style="height: 150px;" class="  square-dot radius" :indicator-dots="true" :autoplay="true">
			<swiper-item style="height: 150px;"  v-for="(item, index) in swiperList" class="radius" :key="index"><image style="height: 150px;width: 100%;" :src="item.pic" mode=""></image></swiper-item>
		</swiper>

		<view v-if="ad.remark" class="tipBox text-center bg-white">
			<view class="tit">{{ ad.remark }}</view>
			<rich-text :nodes="ad.text"></rich-text>
			<!-- 		<view class="text">神童游乐 业内知名品牌</view>
			<view class="text">专业从事室内外乐园，主题公园项目的设计、生产、销售、安装、连锁加盟</view> -->
		</view>

		<view class="categoryMoreBox bg-white flex align-center justify-between">
			<text class="tit">
				特色项目
				<text class="en">CHARACTERISTIC PROJECT</text>
			</text>
			<text @click="gotoCategory" class="more">更多 ></text>
		</view>
		<view class="categoryList  bg-white radius flex ">
			<view
				v-if="index <= 8"
				@click="gotoGoodsList(item.menu_ID)"
				v-for="(item, index) in categoryList"
				:key="index"
				class="item text-center flex flex-direction align-center"
			>
				<image :src="item.menu_IMG" mode="aspectFill"></image>
				<text class="bg-blue textov1">{{ item.menu_NAME }}</text>
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
		</view>

		<view v-if="joinUs[0].pic" class="joinUs bg-white">
			<view class="hot_tit">
				<image style="background-color: transparent;" class="bgimg" src="/static/hotgoods.png" mode=""></image>
				加盟合作
			</view>
			<view class="listBox flex justify-between">
				<view @click="gotoJoinUs(1)" class="item item1 flex flex-direction justify-center align-center bg-gradual-green">
					<image :src="joinUs[0].pic" mode="widthFix"></image>
					开店指导
				</view>
				<view class="item flex flex-direction justify-between">
					<view @click="gotoJoinUs(2)" class="topBox flex flex-direction justify-center align-center bg-gradual-purple">
						<image :src="joinUs[1].pic" mode="widthFix"></image>
						市场前景
					</view>
					<view @click="gotoJoinUs(4)" class="bottomBox flex flex-direction justify-center align-center bg-gradual-pink">
						<image :src="joinUs[3].pic" mode="widthFix"></image>
						加盟条件
					</view>
				</view>
				<view class="item flex flex-direction justify-between">
					<view @click="gotoJoinUs(3)" class="topBox flex flex-direction justify-center align-center bg-gradual-orange">
						<image :src="joinUs[2].pic" mode="widthFix"></image>
						加盟优势
					</view>
					<view @click="gotoJoinUs(5)" class="bottomBox flex flex-direction justify-center align-center bg-gradual-blue">
						<image :src="joinUs[4].pic" mode="widthFix"></image>
						加盟流程
					</view>
				</view>
			</view>
		</view>

		<view v-if="news.length > 0" v-for="(item, index) in news" :key="index" class="newsBox joinUs bg-white">
			<view class="hot_tit">
				<image style="background-color: transparent;" class="bgimg" src="/static/hotgoods.png" mode=""></image>
				{{ item.category }}
			</view>
			<view @click="gotoNewsDetail(list.uuid)" v-for="(list, key) in item.list" :key="key" class="list">
				<view class="tit textov1">{{ list.title }}</view>
				<view class="time">{{ list.time }}</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			swiperList: [],
			categoryList: [],
			hootGoods: [],
			news: [],
			ad: {},
			joinUs: []
		};
	},
	onLoad() {
		this.getBannerList();
		this.getCategory();
		this.getAd();
		this.getGoodsList();
		this.getJoinUsImg();
		this.getNews();
	},
	onShareAppMessage() {
		return {
			title: '首页',
			path: '/pages/index/index?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		getJoinUsImg() {
			this.request({
				url: '/appJoin/getJoin',
				data: {},
				success: res => {
					console.log('joinUs', res);
					this.joinUs = res.data.list;
					this.joinUs = this.joinUs.map(i => {
						i.pic = this.imgUrl + i.pic;
						return i;
					});
				}
			});
		},
		gotoCategory() {
			uni.switchTab({
				url: '/pages/category/category'
			});
		},
		getAd() {
			this.request({
				url: '/appAdvertising/getAdvertising',
				success: res => {
					console.log('ad', res);
					if (res.data.returnCode === 1) {
						this.ad = res.data.obj;
					}
				}
			});
		},

		gotoNewsDetail(id) {
			uni.navigateTo({
				url: '/pages/index/newsDetail?newsId=' + id
			});
		},
		getNews() {
			this.request({
				url: '/appInformation/getInformation',
				data: {},
				success: res => {
					console.log('news', res);
					if (res.data.returnCode === 1) {
						this.news = res.data.list;
					}
				}
			});
		},
		gotoGoodsList(id) {
			uni.navigateTo({
				url: '/pages/category/goodsList?categoryId=' + id
			});
		},
		gotoJoinUs(sortType) {
			uni.navigateTo({
				url: '/pages/my/joinUs?sortType=' + sortType
			});
		},
		getCategory() {
			this.request({
				url: '/appMeun/getTwo',
				data: {},
				success: res => {
					console.log('分类', res);
					if (res.data.returnCode === 1) {
						this.categoryList = res.data.list;
						this.categoryList = this.categoryList.map(i => {
							i.menu_IMG = this.imgUrl + i.menu_IMG;
							return i;
						});
					}
				}
			});
		},
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
	.tipBox {
		padding: 30rpx 20rpx;
		.tit {
			font-size: 28rpx;
			color: #000;
			font-weight: 700;
			margin-bottom: 14rpx;
		}
		.text {
			font-size: 24rxp;
			color: #999;
			line-height: 1.5em;
		}
	}
	.categoryMoreBox {
		padding: 0 20rpx;
		margin-top: 16rpx;
		line-height: 40px;
		.tit {
			font-size: 30rpx;
			color: #000;
			.en {
				font-size: 24rpx;
				color: #999;
				margin-left: 6rpx;
			}
		}
		.more {
			font-size: 24rpx;
			color: #999;
		}
	}
	.categoryList {
		flex-wrap: wrap;
		padding: 0 20rpx;
		padding-bottom: 20rpx;
		.item {
			width: calc(33.3% - 15rpx);
			margin-right: 20rpx;
			height: 100px;
			position: relative;
			margin-bottom: 20rpx;
			&:nth-child(3n) {
				margin-right: 0;
			}
			& > image {
				width: 100%;
				// background-color: transparent;
			}
			& > text {
				width: 100%;
				line-height: 50rpx;
				font-size: 24rpx;
				position: absolute;
				left: 0;
				bottom: 0;
				background-color: rgba(0, 0, 0, 0.4);
				color: #fff;
				
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
	.joinUs {
		padding: 15px 0;
		margin-top: 10px;
		.hot_tit {
			margin: 0 auto;
			margin-bottom: 15px;
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
		.listBox {
			image {
				background-color: transparent;
			}
			.item {
				width: calc(33% - 4rpx);
				height: 170px;
				font-size: 24rpx;
				.topBox,
				.bottomBox {
					height: calc(50% - 4rpx);
					& > image {
						width: 70rpx;
						margin-bottom: 14rpx;
					}
				}
			}
			.item1 {
				& > image {
					width: 100rpx;
					margin-left: 10rpx;
					margin-bottom: 14rpx;
				}
			}
		}
	}

	.newsBox {
		.list {
			padding: 0 15rpx;
			padding-bottom: 15rpx;
			margin-bottom: 15rpx;
			border-bottom: 1rpx dashed #999;
			.tit {
				font-size: 28rpx;
				color: #000;
			}
			.time {
				font-size: 22rpx;
				color: #999;
				margin-top: 4px;
			}
		}
	}
}
</style>
