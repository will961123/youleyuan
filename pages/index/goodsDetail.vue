<template>
	<view class="detailView">
		<swiper style="height: 562rpx;" class="screen-swiper square-dot radius" :indicator-dots="true" :autoplay="true">
			<swiper-item  style="height: 562rpx;" v-for="(item, index) in swiperList" class="radius" :key="index"><image :src="item" mode="scaleToFill"></image></swiper-item>
		</swiper>

		<!-- <view class="titBox ">产品参数</view>
		<view class="parameterBox bg-white">
			<view v-for="(item, index) in goodsInfo.parameter" :key="index" class="list flex">
				<view class="key">{{ item.parname }}</view>
				<view class="val">{{ item.partext }}</view>
			</view>
		</view> -->

		<!-- <view class="titBox ">盈利预估(受地域影响，以下数据仅供参考)</view>
		<view class="profitBox parameterBox bg-white">
			<view v-for="(item, index) in goodsInfo.profit" :key="index" class="list flex">
				<view class="key">{{ item.key }}</view>
				<view class="val">{{ item.val }}</view>
			</view>
		</view> -->

		<view class="titBox ">产品介绍</view>
		<view v-if="goodsInfo.videoSrc" class="videoBox">
			<video object-fit="cover" :src="goodsInfo.videoSrc" controls></video>
			<!-- 			<video object-fit="cover" src="http://data.vod.itc.cn/?new=/63/77/40r0UDo8pmrZC3OPo3q2RF.mp4&vid=103479475&plat=17&mkey=oN0oDPsqaRx67_9m2cgDZPNuYVNVD0UG&ch=null&user=api&uid=1608272337357415&SOHUSVP=lg14XbqHWvxmpskUfi3zCQg0SW6oocozV2YB5CrfiCs&pt=1&prod=56&pg=1&eye=0&cv=1.0.0&qd=68000&src=11050001&ca=4&cateCode=300&_c=1&appid=tv" controls></video> -->
		</view>
		
		<view class="detailBox bg-white"><rich-text v-if="goodsInfo.ritchText" :nodes="goodsInfo.ritchText"></rich-text></view>

		<view class="menuBox bg-cyan flex">
			<view class="item bg-white like flex align-center justify-center">
				<view @click="changeLike(false)" class="flex align-center justify-center" v-if="goodsInfo.like">
					<image src="/static/like.png" mode="aspectFit"></image>
					取消收藏
				</view>
				<view @click="changeLike(true)" class="flex align-center justify-center" v-else>
					<image src="/static/like3.png" mode="aspectFit"></image>
					收藏
				</view>
			</view>
			<!-- <view @click="showAddCommit" class="item commit flex align-center justify-center">
				<image src="/static/commit.png" mode="aspectFit"></image>
				留言
			</view> -->
			<view class="item bg-white search flex align-center justify-center">
				<button open-type="share">分享</button>
				<image src="/static/search2.png" mode="aspectFit"></image>
				分享
			</view>
			<view @click="call" class="item call bg-orange flex align-center justify-center">
				<image src="/static/call2.png" mode="aspectFit"></image>
				联系我们
			</view>
		</view>

		<view v-if="showAddCommitFlag" class="mc">
			<view class="_mcMain bg-white">
				<image @click="showAddCommitFlag = false" src="/static/delect.png" class="close" mode="aspectFit"></image>
				<textarea value="" placeholder="请输入留言" />
				<!-- <input type="text" placeholder="请输入联系方式" v-model="phone" disabled="" /> -->
				<button class="btn cu-btn">提交</button>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			phone: '',
			showAddCommitFlag: false,
			swiperList: [],
			goodsId: '',
			goodsInfo: {
				videoSrc: '',
				ritchText: '',
				like: false,
				parameter: [],
				profit: [
					{
						key: '客单价',
						val: '123'
					},
					{
						key: '日流量',
						val: '123'
					},
					{
						key: '毛利率',
						val: '123'
					}
				]
			}
		};
	},
	onLoad(options) {
		if (options.goodsId) {
			this.goodsId = options.goodsId;
			this.getGoodsInfo();
		}
		this.saveFooter();
		this.getPhone();
	},
	onShareAppMessage() {
		return {
			title: '商品详情',
			path: '/pages/index/goodsDetail?searchUserId=' + this.getUserId() + '&goodsId=' + this.goodsId
		};
	},
	methods: {
		getPhone() {
			this.request({
				url: '/appPhone/getPhone',
				success: res => {
					console.log('phone', res);
					if (res.data.returnCode === 1) {
						this.phone = res.data.obj;
					}
				}
			});
		},
		showAddCommit() {
			let clientopenid = this.getUserId();
			let phone = uni.getStorageSync('phone');
			this.phone = phone;
			if (!clientopenid) {
				uni.showModal({
					title: '登录',
					content: '请先登录',
					success: res => {
						if (res.confirm) {
							uni.switchTab({
								url: '/pages/my/my'
							});
						}
					}
				});
				return false;
			}
			if (!phone) {
				uni.showModal({
					title: '登录',
					content: '请授权手机号',
					success: res => {
						if (res.confirm) {
							uni.switchTab({
								url: '/pages/my/my'
							});
						}
					}
				});
				return false;
			}
			this.showAddCommitFlag = true;
		},
		saveFooter() {
			let clientopenid = this.getUserId();
			if (!clientopenid) {
				return false;
			}
			this.request({
				url: '/appTrack/saveTrack',
				data: {
					commuuid: this.goodsId,
					clientopenid: clientopenid
				},
				success: res => {
					console.log('足迹', res);
				}
			});
		},
		changeLike(type) {
			let clientopenid = this.getUserId();
			if (!clientopenid) {
				uni.showModal({
					title: '登录',
					content: '请先登录',
					success: res => {
						if (res.confirm) {
							uni.switchTab({
								url: '/pages/my/my'
							});
						}
					}
				});
				return false;
			}
			this.showLoading();
			let url = '/appCollect/saveCollect';
			if (!type) {
				url = '/appCollect/deleteCollect';
			}
			this.request({
				url,
				data: {
					commuuid: this.goodsId,
					clientopenid: clientopenid
				},
				success: res => {
					uni.hideLoading();
					console.log('like', res);
					if (res.data.returnCode === 1) {
						this.$set(this.goodsInfo, 'like', !this.goodsInfo.like);
					}
				}
			});
		},
		getGoodsInfo() {
			this.showLoading();
			this.request({
				url: '/appCommodity/getparandpic',
				data: {
					commuuid: this.goodsId,
					clientopenid: this.getUserId()
				},
				success: res => {
					uni.hideLoading();
					console.log('goodsInfo', res);
					// res.data.list = res.data.list.map(i => {
					// 	i.pic = this.imgUrl + i.pic;
					// 	return i;
					// });
					this.swiperList = res.data.obj2.piclis.split('^') 
					this.swiperList = this.swiperList.map(i=>{
						return this.imgUrl + i
					})
					// this.$set(this.goodsInfo, 'parameter', res.data.list2);
					this.$set(this.goodsInfo, 'like', res.data.obj);
					if (res.data.obj2 && res.data.obj2.video) {
						this.$set(this.goodsInfo, 'videoSrc', this.imgUrl + res.data.obj2.video);
					}
					if (res.data.obj2 && res.data.obj2.text) {
						res.data.obj2.text = res.data.obj2.text.replace(/\<img/gi,'<img style="width:100%;height:auto;display:block"')
						this.$set(this.goodsInfo, 'ritchText', res.data.obj2.text);
					}
				}
			});
		},
		call() {
			if (!this.phone) {
				this.showToast('管理员暂未设置电话!');
				return false;
			}
			uni.makePhoneCall({
				phoneNumber: '' + this.phone
			});
		}
	}
};
</script>

<style lang="scss">
.detailView {
	padding-bottom: 40px;
	.titBox {
		height: 60rpx;
		padding: 10rpx 30rpx 10rpx 50rpx;
		font-size: 28rpx;
		position: relative;
		&::after {
			content: '';
			width: 8rpx;
			background-color: red;
			height: 30rpx;
			position: absolute;
			left: 30rpx;
			top: 15rpx;
		}
	}
	.parameterBox {
		.list {
			border-bottom: 1rpx solid #ededed;
			&:last-child {
				border-bottom: none;
			}
			.key,
			.val {
				flex: 1;
				padding: 20rpx 20rpx;
				font-size: 28rpx;
			}
			.key {
				margin-right: -1rpx;
				border-right: 1rpx solid #ededed;
			}
		}
	}
	.videoBox {
		width: 100%;
		& > video {
			width: 100%;
		}
	}
	.detailBox {
		text-align: center;
		padding: 20rpx 25rpx 40rpx 25rpx;
	}
	.menuBox {
		width: 100%;
		position: fixed;
		left: 0;
		bottom: 0;
		height: 40px;
		line-height: 40px;
		text-align: center;
		.item {
			position: relative;
			image {
				width: 44rpx;
				max-height: 44rpx;
				background-color: transparent;
				margin-right: 8rpx;
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
		.like,
		.commit,
		.search {
			flex: 1;
			border-right: 1rpx solid #ededed;
		}
		.call {
			width: 40%;
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
			width: 96%;
			height: 520rpx;
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
			textarea {
				border: 1rpx solid #ededed;
				width: 100%;
				height: 100px;
				padding: 15rpx;
				font-size: 18rpx;
			}
			input {
				border: 1rpx solid #ededed;
				margin-top: 10px;
				height: 30px;
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
				margin-top: 24px;
			}
		}
	}
}
</style>
