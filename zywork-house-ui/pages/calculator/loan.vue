<template>
	<view class="container">
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">建筑面积(平方米)</text>
			<input class="cell-content" type="digit" v-model="loan.houseArea" placeholder="请输入建筑面积(选填)" @input="areaCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房屋单价(元)</text>
			<input class="cell-content" type="digit" v-model="loan.unitPrice" placeholder="请输入房屋单价(选填)" @input="priceCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房屋总价(元)</text>
			<input class="cell-content" type="digit" v-model="loan.totalMoney" placeholder="请输入房屋总价(选填)" @input="totalMoneyCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首付金额(元)</text>
			<input class="cell-content" type="digit" v-model="loan.firstPayMoney" placeholder="请输入首付金额(选填)" @blur="firstPayMoneyCal"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首付比例</text>
			<picker class="cell-content" @change="choosePayPercent" :range="payPercentsDes">
				<input v-model="payPercentsDes[loan.firstPayPercent]" :disabled="true" placeholder="请选择首付比例(选填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">贷款总金额(元)</text>
			<input class="cell-content" type="number" v-model="loan.totalLoan" placeholder="请输入贷款总金额(必填)" @input="loanCal"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">贷款年限</text>
			<picker class="cell-content" @change="chooseLoanYears" :range="loanYearsDes">
				<input v-model="loanYearsDes[loan.loanYear]" :disabled="true" placeholder="请选择贷款年限(必填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">房贷利率</text>
			<picker class="cell-content" @change="chooseYearRate" :range="yearRatesDes">
				<input v-model="yearRatesDes[loan.yearRate]" :disabled="true" placeholder="请选择房贷利率(必填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首次还款日</text>
			<picker class="cell-content" mode="date" @change="bindDateChange">
				<input v-model="loan.firstYearMonth" :disabled="true" placeholder="请选择首次还款年月日(选填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<button class="zy-add-btn" @click="calculate">计算</button>
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
				loan: {
					totalMoney: null,
					totalLoan: null,
					firstPayPercent: null,
					firstPayMoney: null,
					totalLoan: null,
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
				yearRates: [3.92, 4.165, 4.41, 4.655, 4.9, 5.145, 5.39, 5.63, 5.88, 3.25, 3.4125, 3.575],
				yearRatesDes: [ '3.92%(商贷基准利率8折)', '4.165%(商贷基准利率85折)', '4.41%(商贷基准利率9折)', 
				'4.655%(商贷基准利率95折)', '4.9%(商贷基准利率)', '5.145%(商贷基准利率上浮5%)',
				'5.39%(商贷基准利率上浮10%)', '5.63%(商贷基准利率上浮15%)', '5.88%(商贷基准利率上浮20%)',
				'3.25%(公积金基准利率)', '3.4125%(公积金基准利率上浮5%)', '3.575%(公积金基准利率上浮10%)'],
				inputTotalOrFirstPay: null
			}
		},
		methods: {
			choosePayPercent(e) {
				this.loan.firstPayPercent = e.target.value
				if (this.loan.totalMoney) {
					this.loan.firstPayMoney = (this.loan.totalMoney * this.payPercents[this.loan.firstPayPercent] / 100).toFixed(2)
					this.loan.totalLoan = this.loan.totalMoney - this.loan.firstPayMoney
				}
			},
			chooseLoanYears(e) {
				this.loan.loanYear = e.target.value
			},
			chooseYearRate(e) {
				this.loan.yearRate = e.target.value
			},
			bindDateChange: function(e) {
				this.loan.firstYearMonth = e.target.value
			},
			totalMoneyCal(e) {
				if (e.target.value) {
					this.inputTotalOrFirstPay = 'total'
					if (this.loan.firstPayPercent) {
						this.loan.firstPayMoney = (this.loan.totalMoney * this.payPercents[this.loan.firstPayPercent] / 100).toFixed(2)
						this.loan.totalLoan = this.loan.totalMoney - this.loan.firstPayMoney
					}
				} else {
					this.loan.firstPayMoney = null
					this.loan.totalLoan = null
				}
			},
			firstPayMoneyCal(e) {
				if (e.target.value) {
					this.inputTotalOrFirstPay = 'firstPay'
					if (this.loan.totalMoney) {
						let firstPayPercent = (this.loan.firstPayMoney / this.loan.totalMoney * 100).toFixed(2)
						this.loan.totalLoan = this.loan.totalMoney - this.loan.firstPayMoney
						if (this.payPercents.indexOf(firstPayPercent) < 0) {
							this.payPercents.push(firstPayPercent)
							this.payPercentsDes.push(firstPayPercent + '%')
							this.loan.firstPayPercent = this.payPercents.length - 1
						}
					}
				} else {
					this.loan.totalLoan = null
					this.loan.firstPayPercent = null
				}
			},
			loanCal(e) {
				if (e.target.value) {
					if (this.loan.totalMoney && this.inputTotalOrFirstPay === 'total') {
						this.loan.firstPayMoney = this.loan.totalMoney - e.target.value
					} else if (this.loan.firstPayMoney && this.inputTotalOrFirstPay === 'firstPay') {
						this.loan.totalMoney = parseFloat(this.loan.firstPayMoney) + parseFloat(e.target.value)
					}
					if (this.loan.totalMoney && this.loan.firstPayMoney) {
						let firstPayPercent = (this.loan.firstPayMoney / this.loan.totalMoney * 100).toFixed(2)
						if (this.payPercents.indexOf(firstPayPercent) < 0) {
							this.payPercents.push(firstPayPercent)
							this.payPercentsDes.push(firstPayPercent + '%')
							this.loan.firstPayPercent = this.payPercents.length - 1
						}
					}
				} else {
					if (this.loan.totalMoney) {
						this.loan.firstPayMoney = null
					} else if (this.loan.firstPayMoney) {
						this.loan.totalMoney = null
					}
					this.loan.firstPayPercent = null
				}
			},
			areaCal(e) {
				if (e.target.value) {
					if (this.loan.totalMoney) {
						this.loan.unitPrice = (this.loan.totalMoney / e.target.value).toFixed(2)
					}
				} else {
					this.loan.unitPrice = null
				}
			},
			priceCal(e) {
				if (e.target.value) {
					if (this.loan.totalMoney) {
						this.loan.houseArea = (this.loan.totalMoney / e.target.value).toFixed(2)
					}
				} else {
					this.loan.houseArea = null
				}
			},
			calculate() {
				if (!this.loan.totalLoan || !this.loan.loanYear || !this.loan.yearRate) {
					showInfoToast('请注意必填信息哦：贷款总金额、贷款年限和房贷利率')
					return
				}
				this.loan.firstPayPercent = this.payPercents[this.loan.firstPayPercent]
				this.loan.loanYear = this.loanYears[this.loan.loanYear]
				this.loan.yearRate = this.yearRates[this.loan.yearRate]
				uni.navigateTo({
					url: '/pages/calculator/loan-cal?loan=' + JSON.stringify(this.loan)
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

</style>
