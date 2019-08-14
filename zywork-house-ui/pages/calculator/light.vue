<template>
	<view class="container">
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">房屋所在层数</text>
			<input class="cell-content" type="number" v-model="house.floor" placeholder="请输入房屋所在层数(必填)"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">遮阳楼总层数</text>
			<input class="cell-content" type="number" v-model="house.totalFloor" placeholder="请输入遮阳楼总层数(必填)"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">房屋层高(米)</text>
			<input class="cell-content" type="digit" v-model="house.floorHeight" placeholder="请输入房屋层高(必填)"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">楼间距(米)</text>
			<input class="cell-content" type="digit" v-model="house.distance" placeholder="请输入楼间距(必填)"/>
		</view>
		<view class="zy-list-cell b-b m-t" hover-class="cell-hover" :hover-stay-time="50">
			<text style="color: #ff0000;">*</text>
			<text class="cell-tit">房屋所在地址</text>
			<text @click="chooseLocation" class="cell-content">
				{{addressData.name}}
			</text>
			<zywork-icon @click="chooseLocation" type="icondizhi" size="16"></zywork-icon>
		</view>
		<view style="font-size: 22upx; color: #fa436a; width: 100%; text-align: center; padding:20upx;">注：*号部分为必填项。本小程序只计算每天是否有日照，采光影响因素众多，最终结果请以实地考察为准，本小程序不负法律责任</view>
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
				house: {
					floor: null,
					totalFloor: null,
					floorHeight: null,
					distance: null
				},
				addressData: {
					name: '请选择地址',
					address: '请选择地址',
					latitude: null,
					longitude: null
				}
			}
		},
		methods: {
			//地图选择地址
			chooseLocation(){
				let myThis = this
				uni.authorize({
					scope: 'scope.userLocation',
					success() {
						uni.chooseLocation({
							success: (data)=> {
								if (data.name) {
									myThis.addressData.name = data.name
									myThis.addressData.address = data.address
									myThis.addressData.latitude = data.latitude
									myThis.addressData.longitude = data.longitude
								}
							}
						})
					},
					fail() {
						showInfoToast('必须授权才能获取位置信息')
					}
				})
			},
			calculate() {
				if (this.house.floor === null || this.house.totalFloor === null || this.house.floorHeight === null
				|| this.house.distance === null || this.addressData.latitude === null) {
					showInfoToast('请注意必填信息哦')
					return
				}
				uni.navigateTo({
					url: '/pages/calculator/light-cal?house=' + JSON.stringify(this.house) + '&addressData=' + JSON.stringify(this.addressData)
				})
			}
		}
	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';

	page {
		background: #fff;
	}

</style>
