<template>
	<view>
		<view v-if="list.length > 0" class="tit bg-white">{{ list[sortType].title }}</view>
		<view style="padding:  0 25rpx;">
				<rich-text v-if="list.length > 0" :nodes="list[sortType].text"></rich-text>
		</view>
	

		<view @click="call" class="call bg-white flex justify-center align-center"><image src="/static/myicon5.png" mode=""></image></view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			sortType: 1,
			list: [],
			phone:''
		};
	},
	onLoad(options) {
		options.sortType ? (this.sortType = options.sortType) : '';
		this.getList();
		this.getPhone()
	},
	onShareAppMessage() {
		return {
			title: '加盟合作',
			path: '/pages/my/joinUs?searchUserId=' + this.getUserId()
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
		call() {
			if (!this.phone) {
				this.showToast('管理员暂未设置电话!');
				return false;
			}
			console.log(this.phone);
			uni.makePhoneCall({
				phoneNumber: '' + this.phone
			});
		},
		getList() {
			this.showLoading();
			this.request({
				url: '/appJoin/getJoin',
				data: {},
				success: res => {
					console.log('joinUs', res);
					uni.hideLoading();
					if (res.data.returnCode === 1) {
						this.list = res.data.list;
						this.list = this.list.map(i => {
							i.text = i.text.replace(/\<img/gi, '<img style="width:100%;height:auto"');
							return i;
						});
						this.list.unshift([]);
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.call {
	width: 60px;
	height: 60px;
	position: fixed;
	right: 8px;
	top: 40%;
	border-radius: 50%;
	box-shadow: 2px 2px 4px 2px #999;
	& > image {
		width: 45px;
		height: 45px;
		background-color: transparent;
	}
}
.tit {
	line-height: 40px;
	font-weight: 700;
	text-align: center;
	margin-bottom: 8rpx;
}
</style>
