<template>
	<view class="categoryView flex justify-between ">
		<scroll-view class="VerticalNav nav bg-white" scroll-y scroll-with-animation>
			<view class="cu-item" :class="index == nowSelect ? 'text-green cur' : ''" v-for="(item, index) in list" :key="index" @click="changeSelect(index)">{{ item.one }}</view>
		</scroll-view>
		<view class="contentView bg-white">
			<view @click="gotoGoodsList(item.menu_ID)" v-if="list.length > 0" v-for="(item, index) in list[nowSelect].shenMenu" :key="index" class="item">
				<image :src="item.menu_IMG" mode="aspectFill"></image>
				<text class="textov2" >{{ item.menu_NAME }}</text>
			</view> 
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			list: [],
			nowSelect: 0
		};
	},
	onLoad() {
		this.getList();
	},
	methods: {
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
						this.list = res.data.list2;
						this.list = this.list.map(i => {
							i.shenMenu = i.shenMenu.map(i => {
								i.menu_IMG = this.imgUrl + i.menu_IMG;
								return i;
							});
							return i;
						});
					} else {
						this.list = [];
					}
				}
			});
		},
		changeSelect(id) {
			this.nowSelect = id;
		}
	}
};
</script>

<style lang="scss">
page {
	background-color: #e0e0e0;
}
.VerticalNav.nav {
	width: 200rpx;
	height: 100%;
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
}

.VerticalNav.nav .cu-item.cur::after {
	content: '';
	width: 8upx;
	height: 30upx;
	border-radius: 10upx 0 0 10upx;
	position: absolute;
	background-color: currentColor;
	top: 0;
	right: 0upx;
	bottom: 0;
	margin: auto;
}
.categoryView {
	height: 100vh;
	.contentView {
		width: calc(100% - 210rpx);
		overflow-y: auto;
		padding: 20rpx;
		.item { 
			width: 240rpx;
			margin-bottom: 20rpx;
			display: inline-block;
			&:nth-child(2n){
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
