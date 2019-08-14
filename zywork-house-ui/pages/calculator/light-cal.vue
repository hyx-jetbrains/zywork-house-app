<template>
	<view class="container">
		<view class="zy-type-title zy-text-bold">房屋所在地址</view>
		<view class="content">{{addressData.name}}-{{addressData.address}}</view>
		<view class="zy-type-title zy-text-bold">房屋所在纬度</view>
		<view class="content">北纬{{addressData.latitude}}</view>
		<view class="zy-type-title zy-text-bold">房屋所在经度</view>
		<view class="content">东经{{addressData.longitude}}</view>
		<view class="zy-type-title zy-text-bold">冬至日采光情况</view>
		<view class="content">{{isDZOk ? '冬至日有日照' : '冬至日零日照'}}</view>
		<view class="zy-type-title zy-text-bold">有日照日期范围</view>
		<view class="content">{{beginDate + '-' + endDate}}</view>
		<view class="card" style="margin-top: 10upx;">
			<view class="card-title card-title1">
				<text>有日照天数</text>
				<text>{{okCount}}</text>
			</view>
			<view class="card-title card-title2">
				<text>零日照天数</text>
				<text>{{365 - okCount}}</text>
			</view>
			<view class="card-title card-title3">
				<text>日照天数比例</text>
				<text>{{(okCount / 365 * 100).toFixed(2)}}%</text>
			</view>
		</view>
		<view style="padding: 20upx; color: #999999; text-align:center;">国家相关规定：冬至日大于等于1小时日照</view>
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
				house: {
					floor: null,
					totalFloor: null,
					floorHeight: null,
					distance: null
				},
				addressData: {
					name: '请选择地址',
					latitude: null,
					longitude: null
				},
				yearLight: [],
				okCount: 0,
				isDZOk: false,
				beginDate: null,
				endDate: null
			}
		},
		onLoad(options) {
			this.house = JSON.parse(options.house)
			this.addressData = JSON.parse(options.addressData)
			this.calculate()
		},
		methods: {
			calculate() {
				// 太阳高度角sin h＝sin φ sin δ＋cos φ cos δ cos t，正午时，cos t = cos0 = 1
				// 正午太阳高度角sinh =sinφ sinδ+cosφ cosδ=sin h=cos(φ－δ)h=90°-|φ-δ|
				// 其中φ为纬度，δ为太阳赤纬角
				// 太阳赤纬角计算方式：sinδ=0.39795cos[0.98563(N-173)/180*PI] N为积日,就是日期在一年中的序号,比如1月1日是1,平年的12月31日是365
				//  tan(a)=(遮阳楼层-所在层高)*层高/楼间距
				// tan(d)>=tan(a) d为冬至日太阳高度角，则全年采光无问题
				let winter = '12月22日'
				this.yearLight = []
				let houseDegree = Math.atan((this.house.totalFloor - this.house.floor) * this.house.floorHeight / this.house.distance) / Math.PI * 180
				for (let day = 1; day <= 365; day++) {
					let date = new Date()
					date.setMonth(0)
					date.setDate(day)
					let dateStr = (date.getMonth() + 1) + '月' + date.getDate() + '日'
					// 计算太阳赤纬角
					let tycw = Math.asin(0.39795 * Math.cos(0.98563 * (day - 173) / 180 * Math.PI))
					let tycwDegree = tycw / Math.PI * 180
					// 正午太阳高度角
					let tygdDegree = 90 - Math.abs(parseFloat(this.addressData.latitude) - tycwDegree)
					if (dateStr === winter) {
						if (tygdDegree >= houseDegree) {
							this.isDZOk = true
						}
					}
					
					if (tygdDegree >= houseDegree) {
						// 表示采光无任何影响
						this.yearLight.push({
							dateStr: dateStr,
							lightOk: true
						})
						this.okCount++
						if (this.beginDate === null) {
							this.beginDate = dateStr
						}
					} else {
						// 表示采光有影响
						this.yearLight.push({
							dateStr: dateStr,
							lightOk: false
						})
						if (this.endDate === null) {
							let tempDate = new Date()
							tempDate.setMonth(date.getMonth())
							tempDate.setDate(date.getDate() - 1)
							let dateStr = (tempDate.getMonth() + 1) + '月' + tempDate.getDate() + '日'
							this.endDate = dateStr
						}
					}
				}
				if (this.endDate === null) {
					this.endDate = '12月31日'
				}
				if (this.okCount === 0) {
					this.beginDate = '/'
					this.endDate = '/'
				}
			}
		}
	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';

	page {
		background: #fff;
	}
	
	.content {
		padding: 20upx;
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

</style>
