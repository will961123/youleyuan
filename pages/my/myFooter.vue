<template>
	<view class="listbox">
		<view v-for="(item, index) in list" :key="index" class="item bg-white flex radius">
			<image :src="item.pic" mode="aspectFit"></image>
			<view class="infoBox flex flex-direction justify-between">
				<view class="title">{{ item.name }}</view>
				<view class="moneyBox flex  align-center justify-between "> 
					<text class="text-red">￥{{ item.price }}</text>
					<button @click="del(item.id)" class="btn cu-btn">删除足迹</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			list: new Array(3).fill({ pic: '/static/logo.png', name: '莫兰迪淘气堡乐园', price: 99999, id: 123 })
		};
	},
	methods:{
		del(id){
			uni.showModal({
				title:'取消收藏',
				content:"确定要删除足迹吗?"
			})
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
		&>image{
			 width: 160rpx;
			 height: 160rpx;
			 margin-right: 22rpx;
		}
	}
	.infoBox{
		flex: 1;
		.title{
			font-weight: 700;
			font-size: 28rpx;
		}
		.moneyBox{
			font-size: 26rpx;
			.btn{
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
