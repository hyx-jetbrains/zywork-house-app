<template>
	<view class="container">
		<view class="zy-menu-cell bg-img shadow-blur" 
		style="background-image:url(https://image.weilanwl.com/color2.0/plugin/wdh2236.jpg);"
		@click="toLoanPage">
			<view class="content">
				<text>房贷计算</text>
			</view>
		</view>
		<view class="zy-menu-cell bg-img shadow-blur" style="background-image:url(https://image.weilanwl.com/color2.0/plugin/qpct2148.jpg);">
			<view class="content">
				<text>采光计算</text>
			</view>
		</view>
		<view class="zy-menu-cell bg-img shadow-blur" style="background-image:url(https://image.weilanwl.com/color2.0/plugin/qpczdh2307.jpg);">
			<view class="content">
				<text>备案查询(赣州)</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {}
		},
		methods: {
			toLoanPage() {
				uni.navigateTo({
					url: '/pages/calculator/loan'
				})
			}
		}
	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';

	page {
		background: #ffffff;
	}
	
	.container {
		padding: 20upx;
	}
	
	.zy-menu-cell {
		width: 100%;
		height: 200upx;
		display: flex;
		flex-direction: row;
		align-items: center;
		text-align: center;
		margin-bottom: 20upx;
		border-radius: 20upx;
		
		.content {
			width: 100%;
			display: flex;
			flex-direction: column;
			align-items: center;
		}
		
		text {
			font-size: 40upx;
			color: #ffffff;
			font-weight: bold;
			transform: skew(-10deg, 0deg);
			text-shadow: 0px 0px 6upx rgba(0, 0, 0, 0.3)
		}
	}
	
	.bg-img {
			background-size: cover;
			background-position: center;
			background-repeat: no-repeat;
		}
		
		.shadow-blur {
			position: relative;
		}
		
		.shadow-blur::before {
			content: "";
			display: block;
			background: inherit;
			filter: blur(10upx);
			position: absolute;
			width: 100%;
			height: 100%;
			top: 10upx;
			left: 10upx;
			z-index: -1;
			opacity: 0.4;
			transform-origin: 0 0;
			border-radius: inherit;
			transform: scale(1, 1);
		}
</style>
