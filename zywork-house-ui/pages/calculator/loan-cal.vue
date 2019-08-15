<template>
	<view class="container">
		<!-- 导航拦 -->
		<view class="navbar">
			<view v-for="(item, index) in navList" :key="index" class="nav-item" :class="{current: tabCurrentIndex === index}"
			 @click="tabClick(index)">
				{{item.text}}
			</view>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">贷款总金额(元)</text>
			<input class="cell-content" v-model="loan.totalLoan" :disabled="true"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">贷款年限</text>
			<input class="cell-content" v-model="loanYearsDes[loan.loanYear]" :disabled="true"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房贷利率</text>
			<input class="cell-content" v-model="yearRatesDes[loan.yearRate]" :disabled="true"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首次还款日</text>
			<input class="cell-content" v-model="loan.firstYearMonth" :disabled="true"/>
		</view>
		<view class="card">
			<view class="card-title card-title1">
				<text>总还款</text>
				<text>¥{{payment.totalPayment}}</text>
			</view>
			<view class="card-title card-title2">
				<text>总本金</text>
				<text>¥{{payment.totalPrincipal}}</text>
			</view>
			<view class="card-title card-title3">
				<text>总利息</text>
				<text>¥{{payment.totalInterest}}</text>
			</view>
		</view>
		<swiper :current="tabCurrentIndex" class="swiper-box" duration="300" @change="changeTab">
			<swiper-item class="tab-content" v-for="(tabItem,tabIndex) in navList" :key="tabIndex">
				<scroll-view class="list-scroll-content" scroll-y @scrolltolower="loadData('reachBottom')">
					<view class="month-item" v-for="(item, index) in payment.payMonth" :key="index">
						<view class="date-title">{{item.yearMonthDate}}</view>
						<view class="month-data">
							<view style="width:50%; color: #fa436a;">当月还款：{{item.monthPay}}</view>
							<view style="width:50%; color: #fa436a;">剩余本金：{{item.totalPrincipal}}</view>
						</view>
						<view class="month-data">
							<view style="width:50%;">当月本金：{{item.monthPrincipal}}</view>
							<view style="width:50%;">当月利息：{{item.monthInterest}}</view>
						</view>
						<view class="month-data">
							<view style="width:50%;">总还本金：{{item.totalPayment}}</view>
							<view style="width:50%;">总还利息：{{item.totalInterest}}</view>
						</view>
						<view class="month-data">
							<view style="width:50%;">总还本息：{{item.totalPay}}</view>
						</view>
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	import zyworkIcon from '@/components/zywork-icon/zywork-icon.vue'
	import {
		showInfoToast
	} from '@/common/util.js'
	export default {
		components: {
			zyworkIcon,
		},
		data() {
			return {
				tabCurrentIndex: 0,
				navList: [{
						text: '等额本息'
					},
					{
						text: '等额本金'
					}
				],
				loan: {
					totalMoney: null,
					totalLoan: null,
					firstPayPercent: null,
					firstPayMoney: null,
					houseArea: null,
					unitPrice: null,
					loanYear: null,
					yearRate: null,
					firstYearMonth: null
				},
				payPercents: [15, 20, 25, 30, 35, 40, 45, 50, 60, 70, 80],
				payPercentsDes: ['15%', '20%', '25%', '30%', '35%', '40%', '45%', '50%', '60%', '70%', '80%'],
				loanYears: [30, 25, 20, 15, 10, 5],
				loanYearsDes: ['30年', '25年', '20年', '15年', '10年', '5年'],
				yearRates: [3.92, 4.165, 4.41, 4.655, 4.9, 5.145, 5.39, 5.63, 5.88, 6.125, 6.37, 6.615, 6.86, 3.25, 3.4125, 3.575],
				yearRatesDes: [ '3.92%(商贷基准利率8折)', '4.165%(商贷基准利率85折)', '4.41%(商贷基准利率9折)', 
				'4.655%(商贷基准利率95折)', '4.9%(商贷基准利率)', '5.145%(商贷基准利率上浮5%)',
				'5.39%(商贷基准利率上浮10%)', '5.63%(商贷基准利率上浮15%)', '5.88%(商贷基准利率上浮20%)',
				'6.125%(商贷基准利率上浮25%)', '6.37%(商贷基准利率上浮30%)', '6.615%(商贷基准利率上浮35%)',
				'6.86%(商贷基准利率上浮40%)',
				'3.25%(公积金基准利率)', '3.4125%(公积金基准利率上浮5%)', '3.575%(公积金基准利率上浮10%)'],
				payment: {
					totalPayment: 0, // 总还款
					totalPrincipal: 0, // 总本金
					totalInterest: 0, // 总利息
					payMonth: []
				},
				firstTime: true
			}
		},
		onLoad(options) {
			this.loan = JSON.parse(options.loan)
			let firstMonthDate = new Date(Date.parse(this.loan.firstYearMonth.replace(/-/g, '/')))
			this.debx(parseFloat(this.loan.totalLoan), this.loanYears[this.loan.loanYear] * 12, this.yearRates[this.loan.yearRate] / 100, firstMonthDate)
		},
		methods: {
			//swiper 切换
			changeTab(e) {
				const index = e.target.current
				this.tabClick(index)
			},
			//顶部tab点击
			tabClick(index) {
				if (this.tabCurrentIndex !== index) {
					this.tabCurrentIndex = index
					let firstMonthDate = new Date(Date.parse(this.loan.firstYearMonth.replace(/-/g, '/')))
					if (index === 0) {
						this.debx(parseFloat(this.loan.totalLoan), this.loanYears[this.loan.loanYear] * 12, this.yearRates[this.loan.yearRate] / 100, firstMonthDate)
					} else if (index === 1) {
						this.debj(parseFloat(this.loan.totalLoan), this.loanYears[this.loan.loanYear] * 12, this.yearRates[this.loan.yearRate] / 100, firstMonthDate)
					} 
				}
			},
			getYearMonth(date, addMonth) {
				let temp = new Date()
				temp.setFullYear(date.getFullYear())
				temp.setMonth(date.getMonth())
				temp.setMonth(temp.getMonth() + addMonth)
				temp.setDate(date.getDate())
				return temp.getFullYear() + '-' + (temp.getMonth() + 1 > 9 ? temp.getMonth() + 1 : '0' + (temp.getMonth() + 1)) + '-' + (temp.getDate() > 9 ? temp.getDate() : ('0' + temp.getDate()))
			},
			debx(loanTotal, totalMonth, yearRate, firstMonthDate) {
				uni.showLoading({
					title: '计算中...'
				})
				this.payment.payMonth = []
				this.payment.totalPrincipal = loanTotal
				let monthRate = yearRate / 12
				let rateFactor = Math.pow((1 + monthRate), totalMonth)
				let monthPay = loanTotal * monthRate * rateFactor / (rateFactor - 1)

				// 已还总利息
				let totalInterest = 0
				// 未还总本金
				let totalPrincipal = loanTotal
				// 已还总本金
				let totalPayment = 0

				for (let i = 0; i < totalMonth; i++) {
					let payMonth = {}
					payMonth.yearMonthDate = this.getYearMonth(firstMonthDate, i) + '（第' + (i + 1) + '期）'
					let monthInterest = (loanTotal - totalPayment) * monthRate
					totalInterest += monthInterest
					// 当月总还款
					payMonth.monthPay = monthPay.toFixed(2)
					// 当月利息
					payMonth.monthInterest = monthInterest.toFixed(2)
					// 当月本金
					payMonth.monthPrincipal = (monthPay - monthInterest).toFixed(2)
					
					let monthPayment = monthPay - monthInterest
					totalPayment += monthPayment
					totalPrincipal = loanTotal - totalPayment
					// 当月已还总本金
					payMonth.totalPayment = totalPayment.toFixed(2)
					// 未还总本金
					payMonth.totalPrincipal = totalPrincipal.toFixed(2)
					// 已还总利息
					payMonth.totalInterest = totalInterest.toFixed(2)
					// 总还本息
					payMonth.totalPay = (parseFloat(payMonth.totalInterest) + parseFloat(payMonth.totalPayment)).toFixed(2)
					this.payment.payMonth.push(payMonth)
				}
				this.payment.totalInterest = parseFloat(this.payment.payMonth[totalMonth - 1].totalInterest)
				this.payment.totalPayment = (parseFloat(this.payment.payMonth[totalMonth - 1].totalPayment) + parseFloat(this.payment.totalInterest)).toFixed(2)
				if (this.firstTime) {
					setTimeout(() => {
						uni.hideLoading()
					}, 2000)
					this.firstTime = false
				} else {
					uni.hideLoading()
				}
				
			},
			debj(loanTotal, totalMonth, yearRate, firstMonthDate) {
				uni.showLoading({
					title: '计算中...'
				})
				this.payment.payMonth = []
				this.payment.totalPrincipal = loanTotal
				let monthRate = yearRate / 12
				// 已还总利息
				let totalInterest = 0
				// 未还总本金
				let totalPrincipal = 0
				// 已还总本金
				let totalPayment = 0

				for (let i = 0; i < totalMonth; i++) {
					let payMonth = {}
					payMonth.yearMonthDate = this.getYearMonth(firstMonthDate, i) + '（第' + (i + 1) + '期）'
					let monthInterest = (loanTotal - totalPayment) * monthRate
					totalInterest += monthInterest
					let monthPrincipal = loanTotal / totalMonth
					let monthPay = monthPrincipal + monthInterest
					// 当月总还款
					payMonth.monthPay = monthPay.toFixed(2)
					// 当月利息
					payMonth.monthInterest = monthInterest.toFixed(2)
					// 当月本金
					payMonth.monthPrincipal = monthPrincipal.toFixed(2)
					totalPayment += monthPrincipal
					totalPrincipal = loanTotal - totalPayment
					// 当月已还总本金
					payMonth.totalPayment = totalPayment.toFixed(2)
					// 未还总本金
					payMonth.totalPrincipal = totalPrincipal.toFixed(2)
					// 已还总利息
					payMonth.totalInterest = totalInterest.toFixed(2)
					// 总还本息
					payMonth.totalPay = (parseFloat(payMonth.totalInterest) + parseFloat(payMonth.totalPayment)).toFixed(2)
					this.payment.payMonth.push(payMonth)
				}
				this.payment.totalInterest = parseFloat(this.payment.payMonth[totalMonth - 1].totalInterest)
				this.payment.totalPayment = (parseFloat(this.payment.payMonth[totalMonth - 1].totalPayment) + parseFloat(this.payment.totalInterest)).toFixed(2)
				uni.hideLoading()
			}
		}
	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';

	page {
		height: 100%;
		background: #ffffff;
	}
	
	.container {
		height: 100%;
	}
	
	.swiper-box {
		height: calc(100% - 280px);
	}
	
	.list-scroll-content {
		height: 100%;
		padding: 20upx;
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
	
	.month-item {
		margin-top: 20upx;
		view {
			padding: 10upx;
		}
	}
	
	.card {
		display: flex;
		justify-content: space-around;
	}
	
	.card-title {
		width: 33%;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 30upx;
		
		text {
			color: #ffffff;
			font-weight: bold;
		}
	}
	
	.card-title1 {
		background-color: #CC3366;
	}
	
	.card-title2 {
		background-color: #CC3399;
	}
	
	.card-title3 {
		background-color: #CC33CC;
	}
	
	.date-title {
		font-weight: bold; 
		text-align: center;
	}
	
	.month-data {
		display: flex; 
		flex-direction: row; 
		align-items: center;
	}

</style>
