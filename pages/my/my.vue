<template>
	<view class="myView">
		<view class="topUinfo">
			<image class="bg" src="/static/my_bg.png" mode="aspectFill"></image>
			<view class="userbox flex justify-between align-center">
				<view class="headerbox  ">
					<image :src="userInfo.avatar ? userInfo.avatar : '/static/logo.png'" mode="aspectFill" style="border-radius:50%;background: #999;"></image>
					<view>
						<view class="uname text-center">{{ userInfo.nickName || '请登录' }}</view>
						<!-- <view class="uphone">{{ userInfo ? userInfo.phone || '暂未设置' : '暂未设置' }}</view> -->
					</view>
				</view>
			</view>
		</view>
		<view class="navgaterList flex flex-wrap bg-white">
			<view @click="handlerClickNavgater(item)" v-for="(item, index) in navgaterList" :key="index" class="item flex flex-direction align-center">
				<image :src="item.icon" mode="aspectFit"></image>
				<text>{{ item.name }}</text>
				<button v-if="item.type === 2" open-type="share">分享</button>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			userInfo: {},
			navgaterList: [
				{
					type: 1,
					name: '我的收藏',
					path: '/pages/my/myCollection',
					icon: '/static/logo.png'
				},
				{
					type: 1,
					name: '我的足迹',
					path: '/pages/my/myFooter',
					icon: '/static/logo.png'
				},
				{
					type: 2,
					name: '分享',
					path: '/pages/my/myColler',
					icon: '/static/logo.png'
				},
				{
					type: 1,
					name: '意见反馈',
					path: '/pages/my/feedBack',
					icon: '/static/logo.png'
				},
				{
					type: 1,
					name: '联系我们',
					path: '/pages/my/aboutUs',
					icon: '/static/logo.png'
				}
			]
		};
	},
	onLoad() {},
	methods: {
		handlerClickNavgater(item) {
			if (item.type === 1) {
				this.navgater(item.path);
			}
		},
		navgater(url) {
			uni.navigateTo({
				url
			});
		}
	}
};
</script>

<style lang="scss">
.myView {
	.topUinfo {
		width: 100%;
		position: relative;
		height: 150px;
		color: #fff;
		.bg {
			width: 100%;
			height: 150px;
			position: absolute;
			left: 0;
			top: 0;
		}
		.userbox {
			position: relative;
			padding: 30rpx 0 30rpx 0rpx;
			.headerbox {
				margin: 0 auto;
				& > image {
					width: 70px;
					height: 70px;
				}
				.uname {
					font-size: 36rpx;
					line-height: 1.8em;
				}
			}
		}
	}
	.navgaterList {
		.item {
			width: calc(100% / 3);
			padding: 26rpx 0;
			border-right: 1rpx solid #eeeeee;
			border-bottom: 1rpx solid #eeeeee;
			position: relative;
			&:nth-child(3n){
				border-right: none;
			}
			& > image {
				width: 60rpx;
				height: 60rpx;
			}
			& > text {
				margin-top: 20rpx;
				color: #666;
				font-size: 24rpx;
			}
			&>button{
				width: 100%;
				height: 100%;
				position: absolute;
				left: 0;
				top: 0;
				opacity: 0;
				visibility: hidden;
			}
		}
	}
}
</style>
