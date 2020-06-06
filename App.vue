<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	@import 'colorui/main.css';
	@import 'colorui/icon.css';
	/*每个页面公共css */
	image{
		// background-color: #e0e0e0;
	}
	page {
		font-family: PingFang-SC-Medium;
		background-color: #f1f1f1;
	}
	.px20{
		padding: 0 20rpx;
	}
	.textov1 {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}
	.textov2 {
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}
	
	.mc {
		._mcMain {
			width: 600rpx;
			border-radius: 20rpx;
			margin: 0 auto;
			margin-top: 20%;
			padding-bottom: 36px;
			& > image {
				width: 12px;
				height: 12px;
				float: right;
				margin: 15px 15px 0 0;
			}
			.tit {
				padding-top: 58px;
				font-size: 36rpx;
				color: #333333;
			}
			.tip {
				font-size: 26rpx;
				line-height: 36rpx;
				letter-spacing: 2px;
				color: #999999;
				margin: 10px 0 20px 0;
			}
			.btn {
				font-size: 16px;
				width: 160px;
				margin: 0 auto;
				text-align: center;
				height: 44px;
				line-height: 44px;
				color: #fff;
				background-image: linear-gradient(-90deg, #ff585f 0%, #ff826a 100%);
				box-shadow: 0px 8px 20px 0px rgba(255, 25, 25, 0.5);
				border-radius: 22px;
			}
		}
	}
</style>
