<template>
	<view class='issue'>
		<view v-for="(item, index) in goodsList" :key="index">
			<view class="zy-display-flex zy-head">
				<view>
					{{item.skuTitle}}
				</view>
				<view class="zy-display-flex-right zy-head-tip">
					{{item.skuInfo}}
				</view>
			</view>
			<view class="issue-head zy-issue-head zy-display-flex">
				<slot name="headPic"></slot>
				<!-- <zywork-icon type="iconyunliankeji-" color="#8A8A8A" size="30" class="zy-icon" /> -->
				<image class="zy-goods-img" :src="localFileStorage ? frontBaseUrl + goodsItem.goodsPicPicUrl : goodsItem.goodsPicPicUrl" mode="aspectFill"></image>
				<view class="zy-display-flex zy-rate">
					<view class="zy-display-flex" @click="switchRateType(index, 0)">
						<zywork-icon type="iconhaoping" :color="item.commentLevel == 0 ? '#dd524d' : '#8A8A8A'" size="24" class="zy-icon" />
						<text :style="{color:(item.commentLevel == 0 ? '#dd524d' : '#8A8A8A')}">好评</text>
					</view>
					<view class="zy-display-flex" @click="switchRateType(index, 1)">
						<zywork-icon type="iconchaping1" :color="item.commentLevel == 1 ? '#dd524d' : '#8A8A8A'" size="24" class="zy-icon" />
						<text :style="{color:(item.commentLevel == 1 ? '#dd524d' : '#8A8A8A')}">中评</text>
					</view>
					<view class="zy-display-flex" @click="switchRateType(index, 2)">
						<zywork-icon type="iconchaping1" :color="item.commentLevel == 2 ? '#dd524d' : '#8A8A8A'" size="24" class="zy-icon" />
						<text :style="{color:(item.commentLevel == 2 ? '#dd524d' : '#8A8A8A')}">差评</text>
					</view>
					
				</view>
			</view>
			<textarea v-if="textareaShow" v-model="item.comments" :placeholder="textareaPlaceholder" />
			<view class="zy-box">
				<view v-for="(image,index_1) in item.imageList" :key="index_1" style="position: relative;">
					<image class="zy-upload-img" :src="image" :data-src="image" @tap="previewImage(index, index_1)"></image>
					<zyworkIcon class="zy-delete-icon" type="iconshanchu" color="#000" size="24" @tap="deleteImage(index, index_1)"></zyworkIcon>
				</view>
				<view class="zy-upload-btn" v-if="imageList.length !== 5" @tap="chooseImage(index)">
					<zyworkIcon type="iconshangchuantupian" color="#909399" size="24"></zyworkIcon>
					添加图片
				</view>
			</view>
			<view class="zy-display-flex zy-issue-rate">
				商品评分：<uni-rate :value="item.commentRate" :max="5" :index="index" @change="rateChange"/>
			</view>
		</view>
		<view class="issue-btn-box">
			<button v-if="submitShow" class="zy-add-btn" @click="doSubmit">{{submitText}}</button>
			<slot name="submit"></slot>
		 </view>
		 
	</view>
</template>
<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue'
	import zyworkIcon from '@/components/zywork-icon/zywork-icon.vue'
	import {
		FRONT_BASE_URL,
		LOCAL_FILE_STORAGE,
		BASE_URL,
		USER_TOKEN_KEY,
		showInfoToast,
		showSuccessToast
	} from '@/common/util.js'
	import * as ResponseStatus from '@/common/response-status.js'
	import {
		sourceType,
		sizeType
	} from '@/common/picker-data.js'
	export default {
		components: {
			uniRate,
			zyworkIcon
		},
		props:{
			headTitleShow:{ //标题
				type:[String,Boolean],
				default:true,
			},
			headTitleValue:{
				type:String,
				default:"描述相符"
			},
			
			starsShow:{
				type:[String,Boolean],
				default:true,
			},
			starsMax:{ // 星星最大个数
				type:[String,Number],
				default:5,
			},
			
			textareaShow:{ // 多行文本显示
				type:[String,Boolean],
				default:true,
			},
			textareaPlaceholder:{
				type:[String],
				default:"宝贝满足你的期待吗？说说你的使用心得，分享给想买的他们吧"
			},
			
			
			submitShow:{ // 发布按钮
				type:[String,Boolean],
				default:true,
			},
			submitText:{
				type:String,
				default:"发布",
			},
			
			infoReceive:{ // 获取值
				type:Object,
				default:function(){
					return {
						score:0,
						textareaValue:""
					}
				}
			}
		},
		computed:{
		},
		data() {
			return {
				frontBaseUrl: FRONT_BASE_URL,
				localFileStorage: LOCAL_FILE_STORAGE,
				goodsList: [],
				sourceTypeIndex: 2,
				sizeTypeIndex: 2,
				count: [1, 2, 3, 4, 5],
				sourceType: sourceType,
				sizeType: sizeType,
				submitFlag: true
			};
		},
		methods: {
			/** 选择图片上传 */
			chooseImage(index) {
				let imageLength = this.goodsList[index].imageList.length;
				if (imageLength === 5) {
					let isContinue = this.isFullImg(index);
					if (!isContinue) {
						return;
					}
				}
				var myThis = this;
				uni.chooseImage({
					sourceType: this.sourceType[this.sourceTypeIndex],
					sizeType: this.sizeType[this.sizeTypeIndex],
					count: imageLength + this.count[this.countIndex] > 5 ? 5 - imageLength : this.count[this.countIndex],
					success: (chooseImageRes) => {
						const tempFilePaths = chooseImageRes.tempFilePaths;
						uni.showLoading({
							title: '正在上传'
						})
						const userToken = uni.getStorageSync(USER_TOKEN_KEY)
						uni.uploadFile({
							url: BASE_URL + '/goods-comment-pic/user/upload-img',
							filePath: tempFilePaths[0],
							name: 'file',
							header: {
								'Authorization': 'Bearer ' + userToken
							},
							success: function (res) {
								const data = JSON.parse(res.data);
								if (data.code = ResponseStatus.OK) {
									showSuccessToast(data.message);
									let path = FRONT_BASE_URL + '/' + data.data;
									myThis.goodsList[index].imageList = myThis.goodsList[index].imageList.concat(path);
								} else {
									showInfoToast(data.message);
								}
							},
							fail: () => {
								networkError()
							},
							complete: () => {
								uni.hideLoading()
							}
						});
					}
				})
			},
			/** 清空图片 */
			isFullImg: function(index) {
				return new Promise((res) => {
					uni.showModal({
						content: "已经有5张图片了,是否清空现有图片？",
						success: (e) => {
							if (e.confirm) {
								this.goodsList[index].imageList = [];
								res(true);
							} else {
								res(false)
							}
						},
						fail: () => {
							res(false)
						}
					})
				})
			},
			/** 预览图片 */
			previewImage: function(index, index_1) {
				uni.previewImage({
					current: index_1,
					urls: this.goodsList[index].imageList
				})
			},
			/**
			 * 删除图片
			 * @param {Object} index
			 */
			deleteImage(index, index_1) {
				this.goodsList[index].imageList.splice(index_1,1);
			},
			/**
			 * 设置评价类型
			 * @param {Object} type 评价类型
			 */
			switchRateType(index, type) {
				this.$set(this.goodsList[index], 'commentLevel', type);
			},
			/**
			 * 星星点击监听
			 */
			rateChange(e, index) {
				this.$set(this.goodsList[e.index], 'commentRate', e.value);
			},
			
			/**
			 * @name 提交
			 */
			doSubmit(){
				if (this.submitFlag) {
					this.submitFlag = false
					this.$emit('submit', this.goodsList)
				}
			}
		},
		created() {
			this.infoReceive.score=this.score
		}
	}
</script>
<style lang='scss'>
	
	$backgroundC:#f9f9f9;
	$borderColor:#f5f5f5;
	$white:#ffffff;
	$fontSize:28upx;
	
	.issue {
		background-color: $backgroundC;
		
		&-head{
			background-color: $white;
			height: 100upx;
			border-top: 1upx solid $borderColor;
			border-bottom: 1upx solid $borderColor;
			padding: 0 25upx;
			
			&-pic{
				width: 70upx;
				height: 70upx;
				border-radius: 50%;
				vertical-align: middle;
				margin-right: 17upx;
			}
			&-title{
				line-height: 100upx;
				font-size: 30upx;
				vertical-align: middle;
				margin-right: 35upx;
			}
			
			&-star-box{
				display: inline-block;
				
				image{
					width: 32upx;
					height: 32upx;
					vertical-align: middle;
					margin-right: 14upx;
				}
				image.active{
					animation: star_move ease-in 1 1s,star_rotate ease 1.5s infinite 1s;
				}
			}
		}
		textarea{
			width: 100%;
			height: 220upx;
			background-color: $white;
			font-size: $fontSize;
			color: #898989;
			padding: 24upx;
			box-sizing: border-box;
			line-height: 40upx
		}
		&-btn-box{
			padding: 54upx 30upx;
		}
	}
	
	@keyframes star_move{
		from{
			width: 50upx;
			height: 50upx;
			transform: rotate(180deg)
		}
		to{
			width: 32upx;
			height: 32upx;
			transform: rotate(0)
		}
	}
	@keyframes star_rotate{
		0%{
			transform: rotateY(360deg)
		}
		100%{
			transform: rotateY(180deg)
		}
	}
	
	.zy-display-flex {
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.zy-display-flex-right {
		margin-left: auto;
		margin-right: 20upx;
	}
	
	.zy-icon {
		display: inline-block; 
		margin-right: 20upx;
	}
	
	.zy-issue-head {
		padding-top: 10upx;
	}
	.zy-issue-rate {
		padding: 10upx 25upx;
		background-color: #FFF;
		font-size: 28upx;
		margin-bottom: 20upx;
	}
	.zy-rate view {
		margin: 0 10upx;
	}
	.zy-rate text {
		font-size: 30upx;
	}
	.zy-add-btn{
		display: flex;
		align-items: center;
		justify-content: center;
		width: 690upx;
		height: 80upx;
		margin: 60upx auto;
		font-size: $font-lg;
		color: #fff;
		background-color: $base-color;
		border-radius: 10upx;
		box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
	}
	.zy-goods-img {
		width: 60upx;
		height: 60upx;
		margin-right: 20upx;
	}
	.zy-head {
		background-color: #FFF;
		padding: 10upx;
		font-size: 28upx;
	}
	.zy-head-tip {
		color: #898989;
	}
	
	.zy-box {
		background-color: #FFF;
		padding: 20upx 10upx 20upx 20upx;
		display: flex;
		align-items: center;
		flex-wrap: wrap; 
		
		
		.zy-delete-icon {
			position: absolute;
			top: -20upx;
			right: -2upx;
			z-index: 999;
		}
		.zy-upload-img {
			width: 170upx;
			height: 170upx;
			margin-right: 10upx;
		}
		.zy-upload-btn  {
			width: 170upx;
			height: 170upx;
			line-height: 50upx;
			border: 1px dashed #909399;
			text-align: center;
			padding-top: 40upx;
			font-size: 24upx;
			color: #909399;
			margin-bottom: 12upx;
		}
	}
</style>
