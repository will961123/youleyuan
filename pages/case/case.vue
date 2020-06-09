<template>
	<view class="caseView">
		<view class="titBox   bg-white">
			<view @click="changeSelectIdx(index)" v-for="(item, index) in list" :key="index" :class="selectIdx === index ? 'item select' : 'item'">{{ item.category }}</view>
		</view>
		<view v-if="list.length > 0" class="richText bg-white">
			<view @click="gotoCaseDetail(item.uuid)" :key="index" v-for="(item, index) in list[selectIdx].list" class="item">
				<view class="imgBox"><image :src="item.pic" mode="aspectFill"></image></view>
				<view class="tit text-center">{{ item.title }}</view>
			</view> 
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
		this.getCase();
	},
	onShareAppMessage() {
		return {
			title: '客户案例',
			path: '/pages/case/case?searchUserId=' + this.getUserId()
		};
	},
	methods: {
		gotoCaseDetail(caseId) {
			uni.navigateTo({
				url: '/pages/case/caseDetail?caseId=' + caseId
			});
		},
		changeSelectIdx(idx) {
			this.selectIdx = idx;
		},
		getCase() {
			this.showLoading();
			this.request({
				url: '/appCase/getCase',
				data: {},
				success: res => {
					console.log('案例', res);
					uni.hideLoading();
					if (res.data.returnCode === 1) {
						this.list = res.data.list;
						this.list = this.list.map(i => {
							i.list = i.list.map(i => {
								i.pic = this.imgUrl + i.pic;
								i.text = i.text.replace(/\<img/gi, '<img style="width:100%;height:auto"');
								return i;
							});
							// i.list = i.list.concat(i.list)
							return i;
						});
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.caseView {
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
	.richText {
		margin-top: 8px;
		padding: 20rpx;
		overflow: hidden;
		.item {
			width: calc(50% - 20rpx);
			// margin-right: 20rpx;
			margin-bottom: 20px;
			float: left;
			&:nth-child(2n) {
				// margin-right: 0;
				float: right;
			}
			.imgBox {
				width: 100%;
				height: 298rpx;
				border: 1rpx solid #ededed;
				padding: 3px;
				display: inline-block;
				& > image {
					width: 100%;
					height: 100%;
				}
			}
			.tit {
				font-size: 24rpx;
				color: #000;
				margin-top: 4px;
			}
		}
	}
}
</style>
