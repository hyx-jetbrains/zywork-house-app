<template>
	<view class="container">
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房屋总价(元)</text>
			<input class="cell-content" type="digit" v-model="loan.totalMoney" placeholder="请输入房屋总价" @input="totalMoneyCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首付金额(元)</text>
			<input class="cell-content" type="digit" v-model="loan.firstPayMoney" placeholder="请输入首付金额" @blur="firstPayMoneyCal"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首付比例</text>
			<picker class="cell-content" @change="choosePayPercent" :range="payPercentsDes">
				<input v-model="payPercentsDes[loan.firstPayPercent]" :disabled="true" placeholder="请选择首付比例"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">贷款总金额(元)</text>
			<text style="color: #ff0000;">*</text>
			<input class="cell-content" type="number" v-model="loan.totalLoan" placeholder="请输入贷款总金额" @input="loanCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">建筑面积(平方米)</text>
			<input class="cell-content" type="digit" v-model="loan.houseArea" placeholder="请输入建筑面积" @input="areaCal"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房屋单价(元)</text>
			<input class="cell-content" type="digit" v-model="loan.unitPrice" placeholder="请输入房屋单价" @input="priceCal"/>
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">贷款年限</text>
			<text style="color: #ff0000;">*</text>
			<picker class="cell-content" @change="chooseLoanYears" :range="loanYearsDes">
				<input v-model="loanYearsDes[loan.loanYear]" :disabled="true" placeholder="请选择贷款年限"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">房贷利率</text>
			<text style="color: #ff0000;">*</text>
			<picker class="cell-content" @change="chooseYearRate" :range="yearRatesDes">
				<input v-model="yearRatesDes[loan.yearRate]" :disabled="true" placeholder="请选择房贷利率"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
		<view class="zy-list-cell b-b" hover-class="cell-hover" :hover-stay-time="50">
			<text class="cell-tit">首次还款日</text>
			<picker class="cell-content" mode="date" @change="bindDateChange">
				<input v-model="loan.firstYearMonth" :disabled="true" placeholder="请选择首次还款年月日"/>
			</picker>
			<zywork-icon type="iconxiangyou" color="#909399" size="12" class="cell-more" />
		</view>
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
				}
			}
		},
		onLoad(options) {
			this.loan = JSON.parse(options.loan)
			console.log(this.loan)
		},
		methods: {
			
		}
	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';

	page {
		background: #ffffff;
	}

</style>
