<template>
	<view class="content">
		<!-- 导航拦 -->
		<view class="navbar">
			<view v-for="(item, index) in navList" :key="index" class="nav-item" :class="{current: tabCurrentIndex === index}"
			 @click="tabClick(index)">
				{{item.text}}
			</view>
		</view>

		<swiper :current="tabCurrentIndex" class="swiper-box" duration="300" @change="changeTab">
			<swiper-item class="tab-content" v-for="(tabItem,tabIndex) in navList" :key="tabIndex">
				<scroll-view class="list-scroll-content" scroll-y @scrolltolower="loadData('reachBottom')">
					<!-- 空白页 -->
					<empty v-if="tabItem.teamList.length === 0"></empty>

					<!-- 成员列表 -->
					<view v-for="(item,index) in tabItem.teamList" :key="index" class="order-item">
						<view class="i-top b-b">
							<image class="portrait" :src="item.headicon || defaultIcon" mode="aspectFill"></image>
							<view>
								<view class="zy-text-bold" style="font-size: 38upx;">{{item.nickname || '暂无昵称'}}</view>
								<view>{{item.createTime}}</view>
							</view>
						</view>
					</view>

					<uni-load-more :status="tabItem.loadingType"></uni-load-more>

				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	import empty from "@/components/empty";
	import Json from '@/Json';
	import {
		ADD_EVALUATE_PAGE
	} from '@/common/page-url.js';
	import {
		doPostForm,
		showInfoToast,
		nullToStr,
		DEFAULT_HEADICON,
		FRONT_BASE_URL
	} from '@/common/util.js';
	import * as ResponseStatus from '@/common/response-status.js';
	export default {
		components: {
			uniLoadMore,
			empty
		},
		data() {
			return {
				defaultIcon: DEFAULT_HEADICON,
				tabCurrentIndex: 0,
				navList: [{
						state: 0,
						text: '一级',
						loadingType: 'more',
						teamList: []
					},
					{
						state: 1,
						text: '二级',
						loadingType: 'more',
						teamList: []
					}
				],
				teamCount: [],
				urls: {
					belowUrl: '/distribution/user/below'
				},
				pager: {
					pageNo: 1,
					pageSize: 10,
					levels: [2]
				}
			};
		},

		onLoad(options) {
			this.teamCount = JSON.parse(options.teamCount)
			this.navList[0].text = '一级(' + (this.teamCount[0] || 0) + ')'
			this.navList[1].text = '二级(' + (this.teamCount[1] || 0) + ')'
			this.loadData('init');
		},
		//下拉刷新
		onPullDownRefresh() {
			this.pager.pageNo = 1;
			this.loadData('pullDown');
		},
		//加载更多
		onReachBottom() {
			this.pager.pageNo += 1;
			this.loadData('reachBottom');
		},
		methods: {
			//获取成员列表
			loadData(type) {
				//这里是将成员挂载到tab列表下
				let index = this.tabCurrentIndex;
				let navItem = this.navList[index];
				if (navItem.loadingType === 'loading') {
					//防止重复加载
					return;
				}
				if (navItem.loadingType === 'nomore') {
					return
				}
				if (type === 'reachBottom') {
					this.pager.pageNo += 1
				}
				navItem.loadingType = 'loading';
				if (this.tabCurrentIndex === 0) {
					this.pager.levels[0] = 2
				}
				if (this.tabCurrentIndex === 1) {
					this.pager.levels[0] = 3
				}
				doPostForm(this.urls.belowUrl, this.pager, {}, true).then(response => {
					let [error, res] = response;
					if (res.data.code === ResponseStatus.OK) {
						// 判断是否还有数据， 有改为 more， 没有改为noMore 
						navItem.loadingType = 'more';
						if (this.pager.pageNo * this.pager.pageSize >= res.data.data.total) {
							navItem.loadingType = 'nomore';
						}
						let tempRows = nullToStr(res.data.data.rows);
						let rows = []
						tempRows.forEach(item => {
							let headicon = item.headicon
							if (headicon) {
								if (headicon.indexOf('http') < 0) {
									headicon = FRONT_BASE_URL = headicon
								}
							}
							rows.push(item)
						})
						if (type === 'init') {
							navItem.teamList = rows;
						} else if (type === 'pullDown') {
							uni.stopPullDownRefresh();
							navItem.teamList = rows;
						} else if (type === 'reachBottom') {
							if (rows.length > 0) {
								navItem.teamList = navItem.teamList.concat(rows);
							}
						}
					} else {
						showInfoToast(res.data.message);
					}
				}).catch(err => {
					console.log(err);
				})
			},
			//swiper 切换
			changeTab(e) {
				const index = e.target.current;
				this.tabClick(index);
			},
			//顶部tab点击
			tabClick(index) {
				if (this.tabCurrentIndex !== index) {
					this.tabCurrentIndex = index
					this.pager.pageNo = 1
					this.navList[index].loadingType = 'more'
					this.loadData('init');
				}
			}
		}

	}
</script>

<style lang="scss">
	page,
	.content {
		background: $page-color-base;
		height: 100%;
	}

	.swiper-box {
		height: calc(100% - 40px);
	}

	.list-scroll-content {
		height: 100%;
	}

	.navbar {
		display: flex;
		height: 40px;
		padding: 0 5px;
		background: #fff;
		box-shadow: 0 1px 5px rgba(0, 0, 0, .06);
		position: relative;
		z-index: 10;

		.nav-item {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			font-size: 15px;
			color: $font-color-dark;
			position: relative;

			&.current {
				color: $base-color;

				&:after {
					content: '';
					position: absolute;
					left: 50%;
					bottom: 0;
					transform: translateX(-50%);
					width: 44px;
					height: 0;
					border-bottom: 2px solid $base-color;
				}
			}
		}
	}

	.uni-swiper-item {
		height: auto;
	}

	.order-item {
		display: flex;
		flex-direction: column;
		padding-left: 30upx;
		background: #fff;
		margin-top: 16upx;

		.i-top {
			display: flex;
			align-items: center;
			padding: 30upx;
			font-size: $font-base;
			color: $font-color-dark;
			position: relative;

			.time {
				flex: 1;
			}

			.state {
				color: $base-color;
			}

			.del-btn {
				padding: 10upx 0 10upx 36upx;
				font-size: $font-lg;
				color: $font-color-light;
				position: relative;

				&:after {
					content: '';
					width: 0;
					height: 30upx;
					border-left: 1px solid $border-color-dark;
					position: absolute;
					left: 20upx;
					top: 50%;
					transform: translateY(-50%);
				}
			}
		}

		/* 多条商品 */
		.goods-box {
			height: 160upx;
			padding: 20upx 0;
			white-space: nowrap;

			.goods-item {
				width: 120upx;
				height: 120upx;
				display: inline-block;
				margin-right: 24upx;
			}

			.goods-img {
				display: block;
				width: 100%;
				height: 100%;
			}
		}

		/* 单条商品 */
		.goods-box-single {
			display: flex;
			padding: 20upx 0;

			.goods-img {
				display: block;
				width: 120upx;
				height: 120upx;
			}

			.right {
				flex: 1;
				display: flex;
				flex-direction: column;
				padding: 0 30upx 0 24upx;
				overflow: hidden;

				.title {
					font-size: $font-base + 2upx;
					color: $font-color-dark;
					line-height: 1;
				}

				.attr-box {
					font-size: $font-sm + 2upx;
					color: $font-color-light;
					padding: 10upx 12upx;
				}

				.price {
					font-size: $font-base + 2upx;
					color: $font-color-dark;

					&:before {
						content: '￥';
						font-size: $font-sm;
						margin: 0 2upx 0 8upx;
					}
				}
			}
		}

		.price-box {
			display: flex;
			justify-content: flex-end;
			align-items: baseline;
			padding: 20upx 30upx;
			font-size: $font-sm + 2upx;
			color: $font-color-light;

			.num {
				margin: 0 8upx;
				color: $font-color-dark;
			}

			.price {
				font-size: $font-lg;
				color: $font-color-dark;

				&:before {
					content: '￥';
					font-size: $font-sm;
					margin: 0 2upx 0 8upx;
				}
			}
		}

		.action-box {
			display: flex;
			justify-content: flex-end;
			align-items: center;
			height: 100upx;
			position: relative;
			padding-right: 30upx;
		}

		.action-btn {
			width: 160upx;
			height: 60upx;
			margin: 0;
			margin-left: 24upx;
			padding: 0;
			text-align: center;
			line-height: 60upx;
			font-size: $font-sm + 2upx;
			color: $font-color-dark;
			background: #fff;
			border-radius: 100px;

			&:after {
				border-radius: 100px;
			}

			&.recom {
				background: #fff9f9;
				color: $base-color;

				&:after {
					border-color: #f7bcc8;
				}
			}
		}
	}


	

	.portrait {
		width: 130upx;
		height: 130upx;
		border: 5upx solid #fff;
		border-radius: 50%;
		margin-right: 20upx;
	}
</style>
