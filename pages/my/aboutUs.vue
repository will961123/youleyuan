<template>
	<view class="aboutUs">
		<view class="bg-img"><image src="/static/banner.png" mode="aspectFill"></image></view>
		<view class="item bg-white">
			<view class="tit">公司简介</view>
			<view class="main">{{ aboutUs.content }}</view>
		</view>
		<view class="item bg-white">
			<view class="tit">联系方式</view>
			<view class="main flex align-center justify-between">
				<view>
					<view class="phone flex align-center justify-between">
						<text>电话：{{ contact.phone }}</text> 
					</view>
					<view class="name">联系人：{{ contact.name }}</view>
				</view>
				<image style="background-color: transparent;" src="/static/abuotphone.png" mode="aspectFit"></image>
			</view>
		</view>
		
		
		
		<button v-if="contact.phone" @click="call(contact.phone)" class="btn cu-btn">拨打电话</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			aboutUs: {
				content: '公司简介公司简介公司简介公司简介公司简介'
			},
			contact: {
				phone: '123456',
				name: 'name'
			}
		};
	},
	onLoad() {
		// this.getAboutUs();
		// this.findContact()
	},
	methods: {
		call(phoneNumber){
			uni.makePhoneCall({
				phoneNumber
			})
		},
		getAboutUs() {
			this.showLoading();
			this.request({
				url: '',
				data: {},
				success: res => {
					console.log('关于我们', res);
					uni.hideLoading();
					if (res.data.errMsg === 'request:ok') {
						this.aboutUs = res.data[0];
					}
				}
			});
		},

		findContact() {
			this.request({
				url: '',
				data: {},
				success: res => {
					console.log('联系方式', res);
					if (res.data.returnCode === 1) {
						this.contact = res.data.obj;
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.aboutUs {
	padding-bottom: 40rpx;
	.bg-img {
		width: 100%;
		height: 200px;
		& > image {
			width: 100%;
			height: 100%;
		}
	}
	.item {
		margin-bottom: 10px;
		padding: 0 30rpx 0 30rpx;
		font-size: 26rpx; 
		.tit {
			font-size: 28rpx;
			letter-spacing: 1rpx;
			color: #0d0d0d;
			line-height: 112rpx;
			border-bottom: 1rpx solid #ededed;
		}
		.main { 
			padding: 30rpx 0;
			letter-spacing: 1rpx;
			line-height: 1.8em;
			color: #999999;
			& > image {
				width: 40rpx;
				height: 40rpx;
			}
		}
	}
	
	.btn {
		font-size: 16px;
		width: 200px;
		text-align: center;
		height: 44px;
		line-height: 44px;
		color: #fff;
		background-image: linear-gradient(-90deg, #ff585f 0%, #ff826a 100%);
		box-shadow: 0px 8px 20px 0px rgba(255, 25, 25, 0.5);
		border-radius: 22px; 
		display: block;
		margin: 0 auto;
		margin-top: 60rpx;
	}
}
</style>
