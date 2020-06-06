<template>
	<view class="myView">
		<view class="topUinfo">
			<image class="bg" src="/static/my_bg.png" mode="aspectFill"></image>
			<view class="userbox flex justify-between align-center">
				<view class="headerbox  ">
					<image :src="userInfo.avatarUrl ? userInfo.avatarUrl : ''" mode="aspectFill" style="border-radius:50%;background: #999;"></image>
					<view>
						<view v-if="openId && userInfo.nickName" class="uname text-center">{{ userInfo.nickName }}</view>
						<view v-else class="uname text-center">
							点击登录
							<button @getuserinfo="getUserInfo" open-type="getUserInfo">登录</button>
						</view>
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

		<view v-if="showPhone" class="mc">
			<view class="_mcMain bg-white">
				<image @click="showPhone = false" src="/static/delect.png" class="close" mode="aspectFit"></image>
				<!-- <input disabled="true" type="text" placeholder="请输入点击授权手机号" /> -->
				<button open-type="getPhoneNumber" @getphonenumber="getphonenumber" class="btn cu-btn">获取手机号</button>
			</view>
		</view>
		 
	</view>
</template>

<script>
export default {
	data() {
		return {
			phone: '',
			showPhone: false,
			userInfo: {},
			openId: '',
			navgaterList: [
				{
					type: 1,
					name: '我的收藏',
					path: '/pages/my/myCollection',
					icon: '/static/myicon1.png'
				},
				{
					type: 1,
					name: '我的足迹',
					path: '/pages/my/myFooter',
					icon: '/static/myicon2.png'
				},
				{
					type: 2,
					name: '分享',
					path: '/pages/my/myColler',
					icon: '/static/myicon3.png'
				},
				{
					type: 1,
					name: '意见反馈',
					path: '/pages/my/feedBack',
					icon: '/static/myicon4.png'
				},
				{
					type: 1,
					name: '关于我们',
					path: '/pages/my/aboutUs',
					icon: '/static/myicon5.png'
				}
			]
		};
	},
	onLoad() {},
	onShow() {
		let userInfo = uni.getStorageSync('userInfo');
		let openId = uni.getStorageSync('openId');
		let phone = uni.getStorageSync('phone');
		this.userInfo = userInfo || {};
		this.openId = openId || '';
		console.log(openId, phone);
		if (openId && !phone) {
			this.showPhone = true;
		}
	},
	onShareAppMessage() {
		return {
			title: '我的',
			path: '/pages/my/my?searchUserId=' + this.getUserId()  
		};
	},
	methods: {
		getphonenumber(e) {
			this.showLoading();
			console.log('点击按钮', e);
			if (e.detail.errMsg === 'getPhoneNumber:ok') {
				uni.login({
					provider: 'weixin',
					success: res => {
						console.log('code', res);
						let code = res.code;
						this.request({
							url: '/appshenClient/getphone',
							data: {
								wxopenid: this.getUserId(),
								iv: e.detail.iv,
								encrypdata: e.detail.encryptedData,
								code: code
							},
							success: res => {
								uni.hideLoading();
								console.log('phone', res);
								if (res.data.returnCode === 1 || res.data.returnCode === 2) {
									this.showPhone = false;
									this.phone = res.data.obj;
									uni.setStorageSync('phone', res.data.obj);
								} else if (res.data.returnCode === -1 || res.data.returnCode === 3) {
									uni.showModal({
										title: '提示',
										content: res.data.returnStr || '系统异常'
									});
								}
							},
							fail: err => {
								uni.hideLoading();
								uni.showModal({
									title: '提示',
									content: '系统异常'
								});
							}
						});
					}
				});
			} else {
				uni.hideLoading();
				uni.showModal({
					title: '获取手机号',
					content: '请授权获得更好地体验',
					showCancel: false
				});
			}
		},
		getUserInfo(e) {
			this.showLoading();
			console.log('点击按钮', e);
			if (e.detail.errMsg === 'getUserInfo:ok') {
				uni.login({
					provider: 'weixin',
					success: res => {
						console.log('code', res);
						let code = res.code;
						uni.getUserInfo({
							success: res => {
								console.log('userInfo', res);
								uni.setStorageSync('userInfo', res.userInfo);
								let userInfo = res;
								this.request({
									url: '/appshenClient/LoginClient',
									data: {
										iv: userInfo.iv,
										encrypdata: userInfo.encryptedData,
										wxpic: userInfo.userInfo.avatarUrl,
										wxname: userInfo.userInfo.nickName,
										code: code
									},
									success: res => {
										uni.hideLoading();
										console.log('openid', res);
										if (res.data.returnCode === 1) {
											this.userInfo = userInfo.userInfo;
											this.openId = res.data.returnStr;
											uni.setStorageSync('openId', res.data.returnStr);
											this.showPhone = true;
										} else {
											uni.showModal({
												title: '提示',
												content: res.data.returnStr || '系统异常'
											});
										}
									},
									fail: err => {
										uni.hideLoading();
										uni.showModal({
											title: '提示',
											content: '系统异常'
										});
									}
								});
							},
							fail: err => {
								uni.hideLoading();
								console.log('userInfo-err', err);
							}
						});
					}
				});
			} else {
				uni.hideLoading();
				uni.showModal({
					title: '登录',
					content: '请登录获得更好地体验',
					showCancel: false
				});
			}
		},
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
image {
	background-color: #e0e0e0;
}
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
					position: relative;
					& > button {
						width: 100%;
						height: 100%;
						left: 0;
						top: 0;
						position: absolute;
						opacity: 0;
					}
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
			&:nth-child(3n) {
				border-right: none;
			}
			& > image {
				width: 60rpx;
				height: 60rpx;
				background-color: transparent;
			}
			& > text {
				margin-top: 20rpx;
				color: #666;
				font-size: 24rpx;
			}
			& > button {
				width: 100%;
				height: 100%;
				position: absolute;
				left: 0;
				top: 0;
				opacity: 0; 
			}
		}
	}

	.mc {
		position: fixed;
		left: 0;
		top: 0;
		z-index: 2;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.4);
		._mcMain {
			width: 84%;
			height: 370rpx;
			padding: 20rpx 30rpx;
			padding-top: 70rpx;
			position: absolute;
			left: 0;
			top: 0;
			right: 0;
			bottom: 0;
			margin: auto;
			font-size: 18rpx;
			box-sizing: border-box;
			.close {
				width: 30rpx;
				height: 30rpx;
				position: absolute;
				top: -10rpx;
				right: -10rpx;
				background-color: transparent;
			}
			input {
				border: 1rpx solid #ededed;
				margin-top: 10px;
				height: 40px;
				font-size: 22rpx;
				padding: 0 15rpx;
				border-radius: 8rpx;
			}
			.btn {
				font-size: 16px;
				width: 80%;
				text-align: center;
				height: 44px;
				line-height: 44px;
				color: #fff;
				background-image: linear-gradient(-90deg, #ff585f 0%, #ff826a 100%);
				box-shadow: 0px 8px 20px 0px rgba(255, 25, 25, 0.5);
				border-radius: 22px;
				display: block;
				margin: 0 auto;
				margin-top: 44px;
			}
		}
	}
}
</style>
