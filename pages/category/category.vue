<template>
	<view>
		<view v-if="list.length > 0">
			<view class="titBox   bg-white">
				<view @click="changeSelect(0, index)" v-for="(item, index) in list" :key="index" :class="nowSelect[0] === index ? 'item select' : 'item'">{{ item.One }}</view>
			</view>
			<view class="categoryView flex justify-between ">
				<scroll-view class="VerticalNav nav bg-white" scroll-y scroll-with-animation>
					<view class="cu-item" :class="-1 == nowSelect[1] ? ' cur' : ''" @click="changeSelect(1, -1)">全部</view>
					<view
						class="cu-item"
						:class="index == nowSelect[1] ? ' cur' : ''"
						v-for="(item, index) in list[nowSelect[0]].Two"
						v-if="index < list[nowSelect[0]].Two.length - 1"
						:key="index"
						@click="changeSelect(1, index, item.MENU_ID)"
					>
						{{ item.MENU_NAME }}
					</view>
				</scroll-view>
				<view v-if="goodsList.length > 0" class="contentView bg-white">
					<view @click="gotoGoodsDetail(item.uuid)" v-if="list.length > 0" v-for="(item, index) in goodsList" :key="index" class="item">
						<image :src="item.homepage" mode="scaleToFill"></image>
						<text class="textov2">{{ item.title }}</text>
					</view>
				</view>
				<view v-else class="contentView bg-white"><will-nodata tittle="暂无数据"></will-nodata></view>
			</view>
		</view>
		<will-nodata v-else tittle="暂无数据"></will-nodata>
	</view>
</template>

<script>
export default {
	data() {
		return {
			list: [],
			goodsList: [],
			nowSelect: [0, -1]
		};
	},
	onLoad() {
		this.getList();
	},
	onShareAppMessage() {
		return {
			title: '分类',
			path: '/pages/category/category?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		gotoGoodsDetail(id) {
			uni.navigateTo({
				url: '/pages/index/goodsDetail?goodsType=1&goodsId=' + id
			});
		},
		gotoGoodsList(id) {
			uni.navigateTo({
				url: '/pages/category/goodsList?categoryId=' + id
			});
		},
		getList() {
			this.request({
				url: '/appMeun/getlist',
				data: {
					PARENT_ID: 0
				},
				success: res => {
					console.log('category', res);
					if (res.data.returnCode === 1) {
						this.list = res.data.list;
						this.list = this.list.map(i => {
							console.log(i.Two)
							i.allGoodsList =
								i.Two.length > 0 && i.Two && i.Two != null
									? i.Two[i.Two.length - 1].shop && i.Two[i.Two.length - 1].shop != null
										? i.Two[i.Two.length - 1].shop
										: []
									: []; 
							i.allGoodsList = i.allGoodsList.map(i => {
								i.homepage = this.imgUrl + i.homepage;
								return i;
							});
							return i;
						});
						this.goodsList = this.list[0].allGoodsList || [];
					} else {
						this.list = [];
					}
				}
			});
		},
		changeSelect(idx, id, menuid) {
			if (idx === 0) {
				this.nowSelect.splice(1, 1, -1);
				this.goodsList = this.list[id].allGoodsList || [];
			} else if (idx != 0 && id === -1) {
				this.goodsList = this.list[this.nowSelect[0]].allGoodsList || [];
			} else {
				this.goodsList = [];
				this.showLoading();
				this.request({
					url: '/appCommodity/getcomlist',
					data: {
						menuid: menuid
					},
					success: res => {
						uni.hideLoading();
						console.log('二级', res);
						if (res.data.returnCode === 1) {
							res.data.list = res.data.list.map(i => {
								i.homepage = this.imgUrl + i.homepage;
								return i;
							});
							this.goodsList = res.data.list;
						}
					}
				});
			}

			this.nowSelect.splice(idx, 1, id);
		}
	}
};
</script>

<style lang="scss">
page {
	background-color: #f1f1f1;
}
.VerticalNav.nav {
	width: 200rpx;
	height: calc(100vh - 40px);
	white-space: initial;
}

.VerticalNav.nav .cu-item {
	width: 100%;
	text-align: center;
	background-color: #fff;
	margin: 0;
	border: none;
	height: 50px;
	position: relative;
}

.VerticalNav.nav .cu-item.cur {
	background-color: #f1f1f1;
	color: #e54d42;
}

.VerticalNav.nav .cu-item.cur::after {
	content: '';
	width: 8upx;
	height: 30upx;
	border-radius: 10upx 0 0 10upx;
	position: absolute;
	background-color: #e54d42;
	top: 0;
	right: 0upx;
	bottom: 0;
	margin: auto;
}
.titBox {
	white-space: nowrap;
	overflow-x: auto;
	height: 40px;
	width: 100%;
	border-bottom: 1rpx solid #999;
	.item {
		display: inline-block;
		position: relative;
		line-height: 40px;
		font-weight: 700;
		font-size: 24rpx;
		margin: 0 20rpx;
		&::after {
			position: absolute;
			left: 50%;
			bottom: 0;
			width: 0rpx;
			margin-left: -20rpx;
			content: '';
			height: 4px;
			border-radius: 2px;
			transition: all 0.5s;
		}
		&.select {
			font-size: 28rpx;
			color: #e54d42;
			&::after {
				width: 40rpx;
				background-color: #e54d42;
			}
		}
	}
}
.categoryView {
	height: calc(100vh - 40px);
	.contentView {
		width: calc(100% - 210rpx);
		overflow-y: auto;
		padding: 20rpx;
		.item {
			width: 240rpx;
			margin-bottom: 20rpx;
			display: inline-block;
			&:nth-child(2n) {
				float: right;
			}
			& > image {
				width: 240rpx;
				height: 240rpx;
			}
		}
	}
}
</style>
