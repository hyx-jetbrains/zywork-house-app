<template>
	<view class="content">
		<view class="zy-top">
			<view class="zy-text-bold zy-status">
				<text class="iconfont iconchenggong" style="color:#FFF; margin-right: 20upx; font-size: 38upx;"></text>
				交易成功
			</view>
			<view class="zy-memo">去评价下本次的购物体验吧～</view>
			<view class="zy-bottom-btn">
				<Button type="default" class="zy-btn" @click="toIndexPage">返回首页</Button>
				<Button type="default" class="zy-btn" @click="toCommentPage">立即评价</Button>
			</view>
		</view>
	</view>
</template>

<script>
	import zyworkIcon from '@/components/zywork-icon/zywork-icon.vue'
	export default {
		components: {
			zyworkIcon
		},
		data() {
			return {
				orderInfo: {}
			};
		},

		onLoad(options) {
			this.orderInfo = options.orderInfo;
		},
		methods: {
			/**
			 * 返回首页
			 */
			toIndexPage() {
				uni.switchTab({
					url: '/pages/index/index'
				})
			},
			/**
			 * 立即评价页面
			 */
			toCommentPage() {
				const item = JSON.parse(this.orderInfo);
				uni.navigateTo({
					url: '/pages/evaluate/add-evaluate?goodsList=' + JSON.stringify(item.userGoodsOrderItemVOList) + '&orderId=' + item.goodsOrderId
				})
			}
		}

	}
</script>

<style lang="scss">
	@import '@/common/zywork-main.scss';

	page,
	.content {
		background: #F8F8F8;
		height: 100%;
	}

	.zy-top {
		height: 340upx;
		background: linear-gradient(to bottom right, #fa436a, #d06b2f);
		text-align: center;
		padding-top: 20upx;

		.zy-status {
			color: #fff;
			font-size: 38upx;
		}
		
		.zy-memo {
			color: #FFF;
			margin: 30upx 0upx;
		}
		.zy-bottom-btn {
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content : center;
		}
		.zy-btn {
			width: 200upx;
			height: 60upx;
			line-height: 60upx;
			border: none;
			border-radius: 30upx;
			color: #FFF;
			margin: 10upx 20upx;
			background-color:transparent;
			border: 1px solid #FFF;
		}
	}
	
</style>
