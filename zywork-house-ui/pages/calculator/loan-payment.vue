<template>
	<view class="container">
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">贷款总金额(元)</text>
			<input class="cell-content" type="number" v-model="loan.totalLoan" placeholder="请输入贷款总金额(必填)"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">贷款年限</text>
			<picker class="cell-content" @change="chooseLoanYears" :range="loanYearsDes" :value="0">
				<input v-model="loanYearsDes[loan.loanYear]" :disabled="true" placeholder="请选择贷款年限(必填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">房贷利率</text>
			<picker class="cell-content" @change="chooseYearRate" :range="yearRatesDes" :value="4">
				<input v-model="yearRatesDes[loan.yearRate]" :disabled="true" placeholder="请选择房贷利率(必填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">首次还款日</text>
			<picker class="cell-content" mode="date" @change="bindDateChange" :value="getDate()">
				<input v-model="loan.firstYearMonth" :disabled="true" placeholder="请选择首次还款年月日(选填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">提前还款日</text>
			<picker class="cell-content" mode="date" @change="bindDateChange1" :value="getDate()">
				<input v-model="loan.payYearMonth" :disabled="true" placeholder="请选择提前还款年月日(选填)"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view style="font-size: 22upx; color: #fa436a; width: 100%; text-align: center; padding:20upx;">注：*号部分为必填项。最终结果请以银行计算为准，本小程序不负法律责任</view>
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
					totalLoan: null,
					loanYear: 0,
					yearRate: 4,
					firstYearMonth: this.getDate(),
					payYearMonth: this.getDate()
				},
				loanYears: [30, 25, 20, 15, 10, 5],
				loanYearsDes: ['30年', '25年', '20年', '15年', '10年', '5年'],
				yearRates: [3.92, 4.165, 4.41, 4.655, 4.9, 5.145, 5.39, 5.63, 5.88, 3.25, 3.4125, 3.575],
				yearRatesDes: [ '3.92%(商贷基准利率8折)', '4.165%(商贷基准利率85折)', '4.41%(商贷基准利率9折)', 
				'4.655%(商贷基准利率95折)', '4.9%(商贷基准利率)', '5.145%(商贷基准利率上浮5%)',
				'5.39%(商贷基准利率上浮10%)', '5.63%(商贷基准利率上浮15%)', '5.88%(商贷基准利率上浮20%)',
				'3.25%(公积金基准利率)', '3.4125%(公积金基准利率上浮5%)', '3.575%(公积金基准利率上浮10%)']
			}
		},
		methods: {
			chooseLoanYears(e) {
				this.loan.loanYear = e.target.value
			},
			chooseYearRate(e) {
				this.loan.yearRate = e.target.value
			},
			bindDateChange: function(e) {
				this.loan.firstYearMonth = e.target.value
			},
			bindDateChange1: function(e) {
				this.loan.payYearMonth = e.target.value
			},
			calculate() {
				if (this.loan.totalLoan === null || this.loan.loanYear === null || this.loan.yearRate === null 
				|| this.loan.firstYearMonth === null || this.loan.payYearMonth === null) {
					showInfoToast('请注意必填信息哦')
					return
				}
				uni.navigateTo({
					url: '/pages/calculator/loan-payment-cal?loan=' + JSON.stringify(this.loan)
				})
			},
			getDate() {
				let date = new Date()
				let month = date.getMonth() + 2
				let theDate = date.getDate()
				return date.getFullYear() + '-' + (month > 9 ? month : ('0' + month)) + '-' + (theDate > 9 ? theDate : ('0' + theDate))
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
