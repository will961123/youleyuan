<template>
	<view class="aboutUs">
		<view class="titBox   bg-white">
			<view @click="changeSelectIdx(index)" v-for="(item, index) in list" :key="index" :class="selectIdx === index ? 'item select' : 'item'">{{ item.title }}</view>
		</view>
		<view class="richText">
			<rich-text v-if="list.length>0" :nodes="list[selectIdx].text"></rich-text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			list: [],
			selectIdx: 0
		};
	},
	onLoad() {
		this.getAboutUs();
	},
	onShareAppMessage() {
		return {
			title: '关于我们',
			path: '/pages/my/aboutUs?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		changeSelectIdx(idx){
			this.selectIdx = idx
		},
		call(phoneNumber) {
			uni.makePhoneCall({
				phoneNumber
			});
		},
		getAboutUs() {
			this.showLoading();
			this.request({
				url: '/appRegard/getRegard',
				data: {},
				success: res => {
					console.log('关于我们', res);
					uni.hideLoading();
					if (res.data.returnCode === 1) {
						this.list = res.data.list;
						this.list = this.list.map(i=>{
							i.text = i.text.replace(/\<img/gi,'<img style="width:100%;height:auto;display:block"');
							console.log(i)
							return i
						})
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
	.titBox {
		white-space: nowrap;
		overflow-x: auto;
		height: 40px;
		width: 100%;
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
				width: 40rpx;
				margin-left: -20rpx;
				content: '';
				height: 4px;
				border-radius: 2px;
			}
			&.select {
				font-size: 28rpx;
				color: #e54d42;
				&::after {
					background-color: #e54d42;
				}
			}
		}
	}
	.richText{
		margin-top: 8px;
	}
}
</style>
