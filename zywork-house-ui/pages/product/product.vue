<template>
	<view class="container">
		<view class="carousel">
			<swiper indicator-dots circular=true duration="400">
				<swiper-item class="swiper-item" v-for="(item,index) in goodsPics" :key="index">
					<view class="image-wrapper">
						<image :src="localFileStorage ? frontBaseUrl + item.picUrl : item.picUrl" class="loaded" mode="aspectFill"></image>
					</view>
				</swiper-item>
			</swiper>
		</view>
		
		<view class="introduce-section">
			<view class="zy-hot-section">
				<text class="zy-tag-hot" v-if="selectSku.isAgent">代理</text>
				<text class="zy-tag-hot" v-if="selectSku.isGroupon">拼团</text>
				<text class="zy-tag-hot" v-if="selectSku.isSeckill">秒杀</text>
				<text class="zy-tag-hot" v-if="selectSku.isPromotion">促销</text>
				<text class="title">{{selectSku.title || ''}}</text>
			</view>
			<view class="price-box">
				<text class="price-tip">¥</text>
				<text class="price">{{selectSku.salePrice || 0}}</text>
				<text class="m-price">¥{{selectSku.price || 0}}</text>
				<text class="coupon-tip">{{((selectSku.salePrice ? selectSku.salePrice : 0) / (selectSku.price ? selectSku.price : 1) * 10).toFixed(1)}}折</text>
			</view>
			<view class="bot-row">
				<text>销量: {{goodsInfo.goodsInfoSaleCount || 0}}</text>
				<text>库存: {{selectSku.storeCount || 0}}</text>
				<text>浏览量: {{goodsInfo.goodsInfoClickCount ? (goodsInfo.goodsInfoClickCount + 1) : 0}}</text>
			</view>
		</view>
		
		<!--  分享 -->
		<!--
		<view class="share-section" @click="share">
			<view class="share-des">
				<view class="share-icon">
					<text class="iconfont iconxingxing"></text>
					<text style="color: #fff;">返</text>
				</view>
			</view>
			<text class="tit">该商品分享可领49减10红包</text>
			<text class="iconfont iconbangzhu" style="font-size: 24upx;"></text>
			<view class="share-btn">
				立即分享
				<text class="iconfont icon-you"></text>
			</view>
		</view>
		-->
		
		<view class="c-list">
			<view class="c-row b-b" @click="toggleSpec">
				<text class="tit">购买类型</text>
				<view class="con">
					<text class="selected-text" v-for="(sItem, sIndex) in specSelected" :key="sIndex">
						{{sItem.name}}
					</text>
				</view>
				<text class="iconfont iconxiangyou icon-more"></text>
			</view>
			<view class="c-row b-b" @click="toggleSpec">
				<text class="tit">购买数量</text>
				<view class="con">
					<text class="selected-text">{{selectSkuQuantity}}</text>
				</view>
				<text class="iconfont iconxiangyou icon-more"></text>
			</view>
			<!--
			<view class="c-row b-b">
				<text class="tit">优惠券</text>
				<text class="con t-r red">领取优惠券</text>
				<text class="iconfont iconxiangyou icon-more"></text>
			</view>
			<view class="c-row b-b">
				<text class="tit">促销活动</text>
				<view class="con-list">
					<text>新人首单送20元无门槛代金券</text>
					<text>订单满50减10</text>
					<text>订单满100减30</text>
					<text>单笔购买满两件免邮费</text>
				</view>
			</view>
			<view class="c-row b-b">
				<text class="tit">服务</text>
				<view class="bz-list con">
					<text>7天无理由退换货 ·</text>
					<text>假一赔十 ·</text>
				</view>
			</view>
			-->
		</view>
		
		<!-- 店铺 --> 
		<view class="shop-section">
			<image :src="localFileStorage ? frontBaseUrl + goodsInfo.goodsShopLogo : goodsInfo.goodsShopLogo" mode="aspectFill"></image>
		    <view class="right">
				{{goodsInfo.goodsShopTitle || ''}}
		    </view>
		</view>
		
		<!-- 评价 -->
		<view class="eva-section">
			<view class="e-header" @click="toEvaluatePage">
				<text class="tit">评价</text>
				<text>({{commentCount}})</text>
				<text class="tip zy-see-all">查看全部</text>
				<text class="iconfont iconxiangyou icon-more zy-see-all"></text>
			</view>
			<view v-if="commentList.length > 0">
				<view class="eva-box" v-for="(item, index) in commentList" :key="index">
					<image class="portrait" :src="item.userDetailHeadicon" mode="aspectFill"></image>
					<view class="right">
						<text class="name">{{item.userDetailNickname}}</text>
						<text class="con">{{item.goodsCommentComments}}</text>
						<view class="bot">
							<text class="attr">购买类型：{{item.goodsCommentSkuInfo}}</text>
							<text class="time">{{item.goodsCommentCreateTime}}</text>
						</view>
					</view>
				</view>
			</view>
			<view v-else class="eva-box">
				<text class="zy-no-data">暂无评价</text>
			</view>
		</view>
		
		<view class="detail-desc">
			<view class="d-header">
				<text>图文详情</text>
			</view>
			<rich-text :nodes="goodsInfo.goodsInfoIntro" style="font-size: 28upx;"></rich-text>
		</view>
		
		<!-- 底部操作菜单 -->
		<view class="page-bottom">
			<navigator url="/pages/index/index" open-type="switchTab" class="p-b-btn">
				<text class="iconfont iconshouye"></text>
				<text>首页</text>
			</navigator>
			<navigator url="/pages/cart/cart" open-type="switchTab" class="p-b-btn">
				<text class="iconfont icongouwuche"></text>
				<text>购物车</text>
			</navigator>
			<view class="p-b-btn" :class="{active: collectionFlag}" @click="toFavorite">
				<text class="iconfont iconshoucang"></text>
				<text>收藏</text>
			</view>
			
			<view class="action-btn-group">
				<button type="primary" class=" action-btn no-border buy-now-btn" @click="buy">立即购买</button>
				<button type="primary" class=" action-btn no-border add-cart-btn" @click="addCart">加入购物车</button>
			</view>
		</view>
		
		
		<!-- 规格-模态层弹窗 -->
		<view 
			class="popup spec" 
			:class="specClass"
			@touchmove.stop.prevent="stopPrevent"
			@click="toggleSpec"
		>
			<!-- 遮罩层 -->
			<view class="mask"></view>
			<view class="layer attr-content" @click.stop="stopPrevent">
				<view class="a-t">
					<image :src="localFileStorage ? frontBaseUrl + selectSku.picUrl : selectSku.picUrl" mode="aspectFill"></image>
					<view class="right">
						<view class="zy-hot-section">
							<text class="zy-tag-hot" v-if="selectSku.isAgent">代理</text>
							<text class="zy-tag-hot" v-if="selectSku.isGroupon">拼团</text>
							<text class="zy-tag-hot" v-if="selectSku.isSeckill">秒杀</text>
							<text class="zy-tag-hot" v-if="selectSku.isPromotion">促销</text>
							<text class="title">{{selectSku.title || ''}}</text>
						</view>
						<text class="price">¥{{selectSku.salePrice}}</text>
						<text class="stock">库存：{{selectSku.storeCount}}</text>
						<view class="selected">
							已选：
							<text class="selected-text" v-for="(sItem, sIndex) in specSelected" :key="sIndex">
								{{sItem.name}}
							</text>
						</view>
						<view class="selected">
							数量：
							<text class="selected-text">{{selectSkuQuantity}}</text>
						</view>
					</view>
				</view>
				<view v-for="(item,index) in specList" :key="index" class="attr-list">
					<text>{{item.name}}</text>
					<view class="item-list">
						<text 
							v-for="(childItem, childIndex) in specChildList" 
							v-if="childItem.pid === item.id"
							:key="childIndex" class="text tit"
							:class="[{selected: childItem.selected, 'enable-text': !childItem.disable, 'disable-text': childItem.disable}]"
							@click="selectSpec(childIndex, childItem.pid)"
						>
							{{childItem.name}}
						</text>
					</view>
				</view>
				<view class="attr-list quantity">
					<text>数量</text>
					<!-- #ifdef MP || APP-PLUS -->
					<uni-number-box ref="numberBox"
						:min="1" 
						:max="parseInt(selectSku.storeCount)"
						:value="selectSkuQuantity"
						@eventChange="changeQuantity"
					></uni-number-box>
					<!-- #endif -->
					<!-- #ifdef H5 -->
					<uni-number-box ref="numberBox"
						:min="1" 
						:max="parseInt(selectSku.storeCount)"
						:value="selectSkuQuantity"
						:disabled="true"
						@eventChange="changeQuantity"
					></uni-number-box>
					<!-- #endif -->
				</view>
				<button class="btn" @click="toggleSpec">完成</button>
			</view>
		</view>
		<!-- 分享 -->
		<share 
			ref="share" 
			:contentHeight="580"
			:shareList="shareList"
		></share>
	</view>
</template>

<script>
	import share from '@/components/share'
	import uniNumberBox from '@/components/uni-number-box.vue'
	import {doPostJson, doGet, doPostForm, showInfoToast, REFRESH_CART, REFRESH_PRODUCT, HAS_USER_INFO, FRONT_BASE_URL, LOCAL_FILE_STORAGE} from '@/common/util.js'
	import {setProductHistory} from '@/common/storage.js'
	import * as ResponseStatus from '@/common/response-status.js'
	import {
		EVALUATE_PAGE
	} from '@/common/page-url.js'
 	export default{
		components: {
			share,
			uniNumberBox,
		},
		data() {
			return {
				goodsInfo: {},
				goodsPics: [],
				categorySpec: [], // 商品的组合属性（规格属性），按属性正序排列
				// 选择的sku
				selectSku: {
					shopId: 0,
					goodsId: 0,
					skuId: 0,
					title: '',
					picUrl: '',
					price: 0,
					salePrice: 0,
					storeCount: 0,
					isAgent: false,
					isGroupon: false,
					isPromotion: false,
					isSeckill: false
				},
				selectSkuQuantity: 1,
				specClass: 'none',
				specSelected:[], // 选择的sku规格值
				specSelectedStr: '', // 选择的sku规格组成的字符串，如7#黄色-9#S
				specList: [], // id, name，用于存储可选规格的名称
				specChildList: [], // id, pid, name，disable用于存储可选规格的值
				skuSpecs: {}, // {"1":"7#黄色-9#S-","2":"7#白色-9#M-"}用于存储所有SKU的可选规格的字符串，key为sku的id, 并判断用户选择的规格是否有对应的SKU
				favorite: true,
				shareList: [],
				fromSku: false,
				hasUserInfo: false,
				frontBaseUrl: FRONT_BASE_URL,
				localFileStorage: LOCAL_FILE_STORAGE,
				collectionFlag: false,
				collection: {
					goodsId: null,
					goodsSkuId: null,
					shopId: null
				},
				commentCount: 0,
				commentList: [],
				skuActivites: []
			}
		},
		async onLoad(options){
			// #ifdef H5
			let openid = options.openid
			let token = options.token
			if (openid && token) {
				// 公众号授权登录成功返回的openid和token
				uni.setStorageSync(USER_OPENID, openid)
				uni.setStorageSync(USER_TOKEN_KEY, token)
				uni.setStorageSync(HAS_USER_INFO, true)
			}
			// #endif
			let goodsInfoId = options.goodsInfoId
			if (options.goodsSkuId) {
				this.fromSku = true
				this.selectSku.skuId = options.goodsSkuId
			}
			this.loadGoodsPic(goodsInfoId)
			this.loadGoodsInfoById(goodsInfoId)
			this.shareList = await this.$api.json('shareList');
			if (!options.fromPage) {
				// 保存浏览历史
				setProductHistory(goodsInfoId, this.goodsPics[0].picUrl)
			}
		},
		onShow() {
			this.hasUserInfo = uni.getStorageSync(HAS_USER_INFO)
			if (uni.getStorageSync(REFRESH_PRODUCT)) {
				this.goodsPics = []
				this.categorySpec = []
				this.specSelected = []
				this.specSelectedStr = ''
				this.specList = []
				this.specChildList = []
				this.loadGoodsInfoById(this.goodsInfo.goodsInfoId)
				uni.setStorageSync(REFRESH_PRODUCT, false)
			}
		},
		methods:{
			// 加载商品的所有图片
			loadGoodsPic(goodsInfoId) {
				doPostJson('/goods-pic/any/all-cond', {
					goodsId: goodsInfoId,
					sortColumn: 'picOrder',
					sortOrder: 'asc'
				}, {}).then(response => {
					let [error, res] = response
					if (res.data.code === ResponseStatus.OK) {
						this.goodsPics = res.data.data.rows
					} 
				}).catch(error => {
					console.log(error)
				})
			},
			// 加载商品的详细信息，包括所有 SKU 的所有属性值 
			loadGoodsInfoById(goodsInfoId) {
				doPostJson('/goods-sku-attr-val/any/goods-goods-sku-attr', {
					"goodsInfoId": goodsInfoId,
					"sortColumn": "goodsSkuId",
					"sortOrder":"asc"
				}, {}).then(response => {
					let [error, res] = response
					if (res.data.code === ResponseStatus.OK) {
						this.goodsInfo = res.data.data
						this.goodsInfo.goodsInfoIntro = this.goodsInfo.goodsInfoIntro.replace(/\/upload/g, FRONT_BASE_URL + 'upload')
						if (this.goodsInfo.goodsSkuVOList && this.goodsInfo.goodsSkuVOList.length > 0) {
							this.loadSkuActivities(this.goodsInfo.goodsInfoShopId, this.goodsInfo.goodsInfoId, 
							this.getAllSkuIds(this.goodsInfo.goodsSkuVOList)).then(response => {
								let [err, res] = response
								if (res.data.code === ResponseStatus.OK) {
									this.skuActivites = res.data.data
									// 设置被选中的sku信息
									this.setSelectSku()
								}
							}).catch(error => {
								console.log(error)
							})
							
							// 获取商品类目指定的组合属性（规格属性），按属性正序排列
							let firstSkuInfo = this.goodsInfo.goodsSkuVOList[0]
							this.getCategoryAttrGroup(firstSkuInfo.goodsSkuAttrVOList)
							this.loadCommentData()
							this.updateClickCount()
						}
					} else {
						showInfoToast('商品不存在哦')
						setTimeout(function() {
							uni.navigateBack({
							})
						}, 3000)
					}
				}).catch(error => {
					console.log(error)
				})
			},
			getAllSkuIds(skuVOList) {
				let skuIds = ''
				skuVOList.forEach(item => {
					skuIds += item.goodsSkuId + ','
				})
				return skuIds
			},
			// 设置被选中的SKU信息
			setSelectSku() {
				if (!this.selectSku.skuId) {
					// 如果不是由某个具体的SKU进来的，则显示商品的第一个SKU信息
					let skuInfo = this.goodsInfo.goodsSkuVOList[0]
					this.setSkuInfo(skuInfo)
				} else {
					// 否则显示指定的SKU的信息
					for (let skuInfo of this.goodsInfo.goodsSkuVOList) {
						if (skuInfo.goodsSkuId == this.selectSku.skuId) {
							this.setSkuInfo(skuInfo)
							break
						}
					}
				}
			},
			// 从SKU中获取selectSku需要的信息并赋值给selectSku
			setSkuInfo(skuInfo) {
				this.collection.goodsId = this.goodsInfo.goodsInfoId
				this.collection.shopId = this.goodsInfo.goodsInfoShopId
				this.collection.goodsSkuId = this.selectSku.skuId = skuInfo.goodsSkuId
				this.selectSku.picUrl = skuInfo.goodsPicPicUrl
				skuInfo.goodsSkuAttrVOList.forEach((skuAttr, index) => {
					if (skuAttr.goodsAttributeAttrCode == 'title') {
						this.selectSku.title = skuAttr.goodsAttributeValueAttrValue
					} else if (skuAttr.goodsAttributeAttrCode == 'price') {
						this.selectSku.price = skuAttr.goodsAttributeValueAttrValue
					} else if (skuAttr.goodsAttributeAttrCode == 'salePrice') {
						this.selectSku.salePrice = skuAttr.goodsAttributeValueAttrValue
					} else if (skuAttr.goodsAttributeAttrCode == 'storeCount') {
						this.selectSku.storeCount = skuAttr.goodsAttributeValueAttrValue
					}
				})
				this.selectSku.isAgent = false
				this.selectSku.isGroupon = false
				this.selectSku.isSeckill = false
				this.selectSku.isPromotion = false
				for (let item of this.skuActivites) {
					if (item.goodsSkuId === this.selectSku.skuId) {
						if (item.agentCount > 0) {
							this.selectSku.isAgent = true
						} else if (item.grouponCount > 0) {
							this.selectSku.isGroupon = true
						} else if (item.seckillCount > 0) {
							this.selectSku.isSeckill = true
						} else if (item.promotionCount > 0) {
							this.selectSku.isPromotion = true
						}
						break
					}
				}
				// 每个sku都需要判断当前的sku是否有被收藏
				this.isFavorite()
			},
			// 获取类目的组合属性（规格属性）
			getCategoryAttrGroup(goodsSkuAttrVOList) {
				goodsSkuAttrVOList.forEach((goodsSkuAttr, index) => {
					if (goodsSkuAttr.goodsCategoryAttributeIsAttrGroup === 1) {
						this.categorySpec.push({
							goodsAttributeId: goodsSkuAttr.goodsCategoryAttributeAttrId,
							goodsAttributeAttrCode: goodsSkuAttr.goodsAttributeAttrCode,
							goodsAttributeAttrName: goodsSkuAttr.goodsAttributeAttrName
						})
					}
				})
				this.getSkuSpec()
			},
			// 获取sku的所有规格，包括规格名称和规格的值；并且记录所有sku对应的规格字符串，字符串格式参考data部分说明
			getSkuSpec() {
				let idIndex = 1
				let attrValueArray = []
				this.categorySpec.forEach((item, index) => {
					let attrId = item.goodsAttributeId
					// 设置规格名称
					this.specList.push({
						id: attrId,
						name: item.goodsAttributeAttrName
					})
					let attrCode = item.goodsAttributeAttrCode
					// 按照SKU的顺序去获取所有可选的规格值
					this.goodsInfo.goodsSkuVOList.forEach((skuItem, index) => {
						let skuAttrList = skuItem.goodsSkuAttrVOList
						for (let skuAttr of skuAttrList) {
							// 如果sku属性的代码与规格属性代码一致
							if (skuAttr.goodsAttributeAttrCode === attrCode) {
								let attrValue = skuAttr.goodsAttributeValueAttrValue
								this.skuSpecs[skuItem.goodsSkuId] = this.skuSpecs[skuItem.goodsSkuId] === undefined ? 
								attrId + '#' + attrValue + '-' 
								: this.skuSpecs[skuItem.goodsSkuId] + attrId + '#' + attrValue + '-'
								if (attrValueArray.indexOf(attrValue) < 0) {
									attrValueArray.push(attrValue)
									this.specChildList.push({
										id: idIndex,
										pid: attrId,
										name: attrValue
									})
									idIndex++
								}
								break
							}
						}
					})
				})
				// 设置选中的SKU的规格
				this.setSelectSkuSpec()
			},
			// 设置选中的sku的规格
			setSelectSkuSpec() {
				if (!this.fromSku) {
					this.specList.forEach(item=>{
						// specChildList中，每个规格属性的第一个规格值就是第一个SKU的规格 
						for(let cItem of this.specChildList){
							if(cItem.pid === item.id){
								this.$set(cItem, 'selected', true)
								this.specSelected.push(cItem)
								this.specSelectedStr += cItem.pid + '#' + cItem.name + '-'
								break
							}
						}
					})
				} else {
					// 从sku中设置已经选择的规格
					let skuSpecValue = this.skuSpecs[this.selectSku.skuId]
					let skuSpecValArray = skuSpecValue.split('-')
					skuSpecValArray.forEach((skuSpecVal, index) => {
						// 分别获取SKU规格值的父id和规格具体值
						let theVal = skuSpecVal.split('#')[1]
						if (theVal) {
							for(let cItem of this.specChildList){
								if(cItem.name == theVal){
									this.$set(cItem, 'selected', true)
									this.specSelected.push(cItem)
									this.specSelectedStr += cItem.pid + '#' + cItem.name + '-'
									break
								}
							}
						}
					})
				}
			},
			//选择规格
			selectSpec(index, pid){
				// 标记选中的规格是否有对应的sku
				let hasSku = false
				// 清除已选中规格的字符串
				this.specSelectedStr = ''
				let list = this.specChildList
				if (list[index].disable) {
					// 如果选择的规格是不可用的，则直接返回
					return
				}
				this.selectSkuQuantity = 1
				// 把同一个规格名称的所有规格值设置为未选中状态
				list.forEach(item=>{
					if(item.pid === pid){
						this.$set(item, 'selected', false)
					}
				})
				// 把当前选中的规格设置为选中状态
				this.$set(list[index], 'selected', true)
				// 选中的规格清除
				this.specSelected = []
				// 把所有选中状态的规格放入到specSelected中，并组装选中规格的字符串值，字符串格式参考data部分说明
				list.forEach(item=>{ 
					if(item.selected === true){ 
						this.specSelectedStr += item.pid + '#' + item.name + '-'
						this.specSelected.push(item)
					} 
				})
				// 对已经选择的规格进行判断，看是否有对应的skuid与之对应，如果没有，则specSelected中只保留最后一个选择的规格值
				for (let key in this.skuSpecs) {
					if (this.skuSpecs[key] === this.specSelectedStr) {
						// 选择的规格有对应的sku，则把key赋值给selectSku
						this.selectSku.skuId = parseInt(key)
						// 重新设置sku的基本信息
						this.setSelectSku()
						hasSku = true
						break;
					}
				}
				if (!hasSku) {
					// 如果没有对应的规格，则specSelected中只保留最后一个选择的规格值
					this.specSelected = this.specSelected.filter(item => item.id === list[index].id)
				}
				// 去计算规格是否可以进行相互组合，对于不能相互组合的规格，会禁用掉，以不能点击选择
				this.specMatch(list[index], hasSku)
			},
			specMatch(specItem, hasSku) {
				// 用户选择的规格的pid
				let specPid = specItem.pid
				// 用户选择的规格值
				let specValue = specItem.name
				// 把所有规格都设置成禁用状态
				this.specChildList.forEach((item, index) => {
					item.disable = true
				})
				// 对记录的SKU的规格信息进行循环，"7#黄色-9#S-", 3: "7#红色-9#M-", 4: "7#白色-9#S-"
				for (let key in this.skuSpecs) {
					// sku对应的规格字符串
					let skuSpecValue = this.skuSpecs[key]
					// 如果sku对应的规格字符串包含有用户点选的规格值
					if (skuSpecValue.indexOf(specValue) >= 0) {
						// 对sku对应的规格字符串按 - 切分，得到所有规格值的父编号和规格的值，如7#黄色，9#S
						let skuSpecValArray = skuSpecValue.split('-')
						skuSpecValArray.forEach((skuSpecVal, index) => {
							// 分别获取SKU规格值的父id和规格具体值
							let thePid = skuSpecVal.split('#')[0]
							let theVal = skuSpecVal.split('#')[1]
							if (theVal) {
								this.specChildList.forEach((item, index) => {
									// 如果规格值的父id与选择的规格值的父id一致，则这类规格值可选择，规格值disable为false
									if (item.pid === specPid) {
										item.disable = false
									}
									// 如果规格值的父id与选择的规格值的父id不一致，
									// 且规格值的父id与SKU的规格值的父id一致，
									// 且规格值与记录的规格值一致，表明sku拥有此规格值，规格值是可选择的，规格值disable设置为false
									if (item.pid != specPid && item.pid == thePid && item.name == theVal) {
										item.disable = false
									}
									// 如果规格值的父id与选择的规格值的父id不一致，
									// 且规格值的父id与SKU的规格值的父id一致，
									// 且规格值与记录的规格值不一样，表明sku不拥有此规格值，规格值是不可选择的，规格值disable保持为true
									// 但是此时由于规格值可能是被选中状态的，所以当选择新规格值时，没有对应的新的sku，则需要把原先不属于sku的选中状态的规格值设置为未选中状态
									if (item.pid !== specPid && item.pid == thePid && item.name != theVal && !hasSku) {
										if (item.selected) {
											item.selected = false
										}
									}
								})
							}
						})
					}
				}
			},
			changeQuantity(data) {
				this.selectSkuQuantity = data.number
			},
			//规格弹窗开关
			toggleSpec() {
				if (this.specSelected.length !== this.categorySpec.length) {
					showInfoToast('请选择商品规格')
					return
				}
				if(this.specClass === 'show'){
					this.specClass = 'hide';
					setTimeout(() => {
						this.specClass = 'none';
					}, 250);
				}else if(this.specClass === 'none'){
					this.specClass = 'show';
				}
			},
			addCart() {
				if (this.hasUserInfo) {
					if (this.selectSku.skuId === null) {
						showInfoToast('请选择商品规格')
						return
					}
					if (this.selectSku.isAgent) {
						showInfoToast('请直接购买分销商商品')
						return
					}
					doPostJson('/goods-cart/user/save', {
						shopId: this.goodsInfo.goodsInfoShopId,
						goodsId: this.goodsInfo.goodsInfoId,
						goodsSkuId: this.selectSku.skuId,
						quantity: this.selectSkuQuantity
					}, {}, true).then(response => {
						let [error, res] = response
						if (res.data.code === ResponseStatus.OK) {
							uni.setStorageSync(REFRESH_CART, true)
							showInfoToast('已加入购物车')
						} else if (res.data.code === ResponseStatus.AUTHENTICATION_TOKEN_ERROR) {
							showInfoToast('您好像还未登录哦')
						} else if (res.data.code === ResponseStatus.DATA_ERROR) {
							showInfoToast('购物车中的该商品数量已达最大库存量')
						} else {
							showInfoToast('请稍候再加入购物车')
						}
					}).catch(error => {
						console.log(error)
					})
					return
				}
				this.toLogin()
			},
			buy(){
				if (this.hasUserInfo) {
					if (this.selectSku.storeCount == 0) {
						showInfoToast('商品库存不足')
						return
					}
					let url = `/pages/order/createOrder?skuIds=${this.selectSku.skuId}&quantity=${this.selectSkuQuantity}`
					if (this.selectSku.isAgent) {
						url = `/pages/order/createOrder?skuIds=${this.selectSku.skuId}&quantity=${this.selectSkuQuantity}&agentRole=sys_mall_distributor_v1`
					}
					uni.navigateTo({
						url: url
					})
				} else {
					this.toLogin()
				}
			},
			updateClickCount() {
				doGet('/goods-info/any/goods/click-count/' + this.goodsInfo.goodsInfoId, {}).then(response => {}).catch(error => {
					console.log(error)
				})
			},
			//分享
			share(){
				this.$refs.share.toggleMask();	
			},
			// 判断是否有收藏信息
			isFavorite() {
				if (this.hasUserInfo) {
					doPostJson('/goods-collection/user/pager-cond', this.collection, {}, true).then(response => {
						let [error, res] = response
						if (res.data.code === ResponseStatus.OK) {
							if (res.data.data.total > 0) {
								this.collectionFlag = true
							} else {
								this.collectionFlag = false
							}
						}
					}).catch(error => {
						console.log(error)
					})
				}
			},
			//收藏
			toFavorite(){
				if (this.hasUserInfo) {
					let url = ''
					let tip = ''
					if (this.collectionFlag) {
						// 取消收藏
						url = '/goods-collection/user/cancel-collection'
						tip = '取消收藏'
					} else {
						// 收藏
						url = '/goods-collection/user/collection'
						tip = '收藏'
					}
					doPostJson(url, this.collection, {}, true).then(response => {
						let [error, res] = response
						if (res.data.code === ResponseStatus.OK) {
							if (tip === '收藏') {
								this.collectionFlag = true
							} else {
								this.collectionFlag = false
							}
							showInfoToast(tip + '成功')
						} else if (res.data.code === ResponseStatus.AUTHENTICATION_TOKEN_ERROR) {
							showInfoToast('您好像还未登录哦')
						} else {
							showInfoToast('请稍候再操作' + tip)
						}
					}).catch(error => {
						console.log(error)
					})
					return
				}
				this.toLogin()
			},
			toLogin() {
				let url = '/pages/login/login'
				// #ifdef H5
				url += '?fromUrl=/pages/product/product?goodsInfoId=' + this.goodsInfo.goodsInfoId
				// #endif
				uni.navigateTo({
					url: url
				})
			},
			stopPrevent(){},
			/**
			 * 前往商品评论页面
			 */
			toEvaluatePage() {
				uni.navigateTo({
					url: `/pages/evaluate/evaluate?goodsId=${this.goodsInfo.goodsInfoId}&shopId=${this.goodsInfo.goodsInfoShopId}`
				})
			},
			/**
			 * 加载评论信息
			 */
			loadCommentData() {
				let url = '/user-goods-comment/any/pager-cond'
				let pager = {
					pageNo: 1,
					pageSize: 1,
					sortColumn: 'goodsCommentCreateTime',
					sortOrder: 'desc',
					goodsCommentShopId: this.goodsInfo.goodsInfoShopId,
					goodsCommentGoodsId: this.goodsInfo.goodsInfoId
				}
				doPostJson(url, pager, {}, false).then(response => {
					let [error, res] = response;
					if (res.data.code === ResponseStatus.OK) {
						this.commentCount = res.data.data.total;
						this.commentList = res.data.data.rows;
					} else {
						showInfoToast('获取评论信息失败')
					}
				}).catch(err => {
					console.log(err);
				})
			},
			loadSkuActivities(shopId, goodsId, goodsSkuIds) {
				return doPostForm('/goods-info/any/list-sku-count', {
					shopId: shopId,
					goodsId: goodsId,
					goodsSkuIds: goodsSkuIds
				}, {})
			}
		},

	}
</script>

<style lang='scss'>
	@import '@/common/zywork-main.scss';
	page{
		background: $page-color-base;
		padding-bottom: 160upx;
	}
	.icon-you{
		font-size: $font-base + 2upx;
		color: #888;
	}
	.carousel {
		height: 722upx;
		position:relative;
		swiper{
			height: 100%;
		}
		.image-wrapper{
			width: 100%;
			height: 100%;
		}
		.swiper-item {
			display: flex;
			justify-content: center;
			align-content: center;
			height: 750upx;
			overflow: hidden;
			image {
				width: 100%;
				height: 100%;
			}
		}
		
	}
	
	/* 标题简介 */
	.introduce-section{
		background: #fff;
		padding: 20upx 30upx;
		
		.title{
			font-size: 32upx;
			color: $font-color-dark;
			height: 50upx;
			line-height: 50upx;
		}
		.price-box{
			display:flex;
			align-items:baseline;
			height: 64upx;
			padding: 10upx 0;
			font-size: 26upx;
			color:$uni-color-primary;
		}
		.price{
			font-size: $font-lg + 2upx;
		}
		.m-price{
			margin:0 12upx;
			color: $font-color-light;
			text-decoration: line-through;
		}
		.coupon-tip{
			align-items: center;
			padding: 4upx 10upx;
			background: $uni-color-primary;
			font-size: $font-sm;
			color: #fff;
			border-radius: 6upx;
			line-height: 1;
			transform: translateY(-4upx); 
		}
		.bot-row{
			display:flex;
			align-items:center;
			height: 50upx;
			font-size: $font-sm;
			color: $font-color-light;
			text{
				flex: 1;
			}
		}
	}
	/* 分享 */
	.share-section{
		display:flex;
		align-items:center;
		color: $font-color-base;
		background: linear-gradient(left, #fdf5f6, #fbebf6);
		padding: 12upx 30upx;
		.share-des {
			width: 70upx;
			height: 30upx;
			border-radius: 4upx;
			display: flex;
			flex-direction: column;
			align-items: center;
			background-color: $uni-color-primary;
			color: #fff;
			font-size: 22upx;
			.share-icon{
				display: flex;
				flex-direction: row;
				align-items: center;
				.iconxingxing {
					font-size: 24upx;
					margin-right: 10upx;
				}
			}
		}
		.tit{
			font-size: $font-base;
			margin-left:10upx;
		}
		.icon-bangzhu1{
			padding: 10upx;
			font-size: 30upx;
			line-height: 1;
		}
		.share-btn{
			flex: 1;
			text-align:right;
			font-size: $font-sm;
			color: $uni-color-primary;
		}
		.icon-you{
			font-size: $font-sm;
			margin-left: 4upx;
			color: $uni-color-primary;
		}
	}
	
	.c-list{
		font-size: $font-sm + 2upx;
		color: $font-color-base;
		background: #fff;
		.c-row{
			display:flex;
			align-items:center;
			padding: 20upx 30upx;
			position:relative;
		}
		.tit{
			width: 140upx;
		}
		.con{
			flex: 1;
			color: $font-color-dark;
			.selected-text{
				margin-right: 10upx;
			}
		}
		.bz-list{
			height: 40upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			text{
				display: inline-block;
				margin-right: 30upx;
			}
		}
		.con-list{
			flex: 1;
			display:flex;
			flex-direction: column;
			color: $font-color-dark;
			line-height: 40upx;
		}
		.red{
			color: $uni-color-primary;
		}
	}
	
	.zy-no-data {
		color: $font-color-light;
		font-size: 28upx;
	}
	
	/* 评价 */
	.eva-section{
		display: flex;
		flex-direction: column;
		padding: 20upx 30upx;
		background: #fff;
		margin-top: 16upx;
		.e-header{
			display: flex;
			align-items: center;
			height: 70upx;
			font-size: $font-sm + 2upx;
			color: $font-color-light;
			.tit{
				font-size: $font-base + 2upx;
				color: $font-color-dark;
				margin-right: 4upx;
			}
			.tip{
				flex: 1;
				text-align: right;
			}
			.icon-you{
				margin-left: 10upx;
			}
		}
	}
	.eva-box{
		display: flex;
		padding: 20upx 0;
		.portrait{
			flex-shrink: 0;
			width: 80upx;
			height: 80upx;
			border-radius: 100px;
		}
		.right{
			flex: 1;
			display: flex;
			flex-direction: column;
			font-size: $font-base;
			color: $font-color-base;
			padding-left: 26upx;
			.con{
				font-size: $font-base;
				color: $font-color-dark;
				padding: 20upx 0;
			}
			.bot{
				display: flex;
				justify-content: space-between;
				font-size: $font-sm;
				color:$font-color-light;
			}
		}
	}
	/*  详情 */
	.detail-desc{
		background: #fff;
		margin-top: 16upx;
		.d-header{
			display: flex;
			justify-content: center;
			align-items: center;
			height: 80upx;
			font-size: $font-base + 2upx;
			color: $font-color-dark;
			position: relative;
				
			text{
				padding: 0 20upx;
				background: #fff;
				position: relative;
				z-index: 1;
			}
			&:after{
				position: absolute;
				left: 50%;
				top: 50%;
				transform: translateX(-50%);
				width: 300upx;
				height: 0;
				content: '';
				border-bottom: 1px solid #ccc; 
			}
		}
	}
	
	/* 规格选择弹窗 */
	.attr-content{
		padding: 10upx 30upx;
		.a-t{
			display: flex;
			align-items: center;
			image{
				width: 170upx;
				height: 170upx;
				flex-shrink: 0;
				border-radius: 8upx;;
			}
			.right{
				display: flex;
				flex-direction: column;
				padding-left: 24upx;
				font-size: $font-sm + 2upx;
				color: $font-color-base;
				line-height: 42upx;
				.price{
					font-size: $font-lg;
					color: $uni-color-primary;
					margin-bottom: 10upx;
				}
				.selected-text{
					margin-right: 10upx;
				}
			}
		}
		.attr-list{
			display: flex;
			flex-direction: column;
			font-size: $font-base + 2upx;
			color: $font-color-base;
			padding-top: 30upx;
			padding-left: 10upx;
		}
		.quantity {
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content: space-between;
		}
		.item-list{
			padding: 20upx 0 0;
			display: flex;
			flex-wrap: wrap;
			.text{
				display: flex;
				align-items: center;
				justify-content: center;
				background: #eee;
				margin-right: 20upx;
				margin-bottom: 20upx;
				border-radius: 100upx;
				min-width: 60upx;
				height: 60upx;
				padding: 0 20upx;
				font-size: $font-base;
			}
			.selected{
				background: #fbebee;
				color: $uni-color-primary;
				border: 1upx solid $uni-color-primary;
			}
			.enable-text {
				color: $font-color-dark;
			}
			.disable-text {
				color: #ccc;
			}
		}
	}
	
	/*  弹出层 */
	.popup {
		position: fixed;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		z-index: 99;
		
		&.show {
			display: block;
			.mask{
				animation: showPopup 0.2s linear both;
			}
			.layer {
				animation: showLayer 0.2s linear both;
			}
		}
		&.hide {
			.mask{
				animation: hidePopup 0.2s linear both;
			}
			.layer {
				animation: hideLayer 0.2s linear both;
			}
		}
		&.none {
			display: none;
		}
		.mask{
			position: fixed;
			top: 0;
			width: 100%;
			height: 100%;
			z-index: 1;
			background-color: rgba(0, 0, 0, 0.4);
		}
		.layer {
			position: fixed;
			z-index: 99;
			bottom: 0;
			width: 100%;
			min-height: 40vh;
			border-radius: 10upx 10upx 0 0;
			background-color: #fff;
			.btn{
				height: 66upx;
				line-height: 66upx;
				border-radius: 100upx;
				background: $uni-color-primary;
				font-size: $font-base + 2upx;
				color: #fff;
				margin: 30upx auto 20upx;
			}
		}
		@keyframes showPopup {
			0% {
				opacity: 0;
			}
			100% {
				opacity: 1;
			}
		}
		@keyframes hidePopup {
			0% {
				opacity: 1;
			}
			100% {
				opacity: 0;
			}
		}
		@keyframes showLayer {
			0% {
				transform: translateY(120%);
			}
			100% {
				transform: translateY(0%);
			}
		}
		@keyframes hideLayer {
			0% {
				transform: translateY(0);
			}
			100% {
				transform: translateY(120%);
			}
		}
	}
	
	/* 底部操作菜单 */
	.page-bottom{
		position:fixed;
		left: 30upx;
		bottom:30upx;
		z-index: 95;
		display: flex;
		justify-content: center;
		align-items: center;
		width: 690upx;
		height: 100upx;
		background: rgba(255,255,255,.9);
		box-shadow: 0 0 20upx 0 rgba(0,0,0,.5);
		border-radius: 16upx;
		
		.p-b-btn{
			display:flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;
			font-size: $font-sm;
			color: $font-color-base;
			width: 96upx;
			height: 80upx;
			.iconfont{
				font-size: 40upx;
				line-height: 48upx;
				color: $font-color-light;
			}
			&.active, &.active .iconfont{
				color: $uni-color-primary;
			}
			.icon-fenxiang2{
				font-size: 42upx;
				transform: translateY(-2upx);
			}
			.icon-shoucang{
				font-size: 46upx;
			}
		}
		.action-btn-group{
			display: flex;
			height: 76upx;
			border-radius: 100px;
			overflow: hidden;
			box-shadow: 0 20upx 40upx -16upx #fa436a;
			box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
			background: linear-gradient(to right, #ffac30,#fa436a,#F56C6C);
			margin-left: 20upx;
			position:relative;
			&:after{
				content: '';
				position:absolute;
				top: 50%;
				right: 50%;
				transform: translateY(-50%);
				height: 28upx;
				width: 0;
				border-right: 1px solid rgba(255,255,255,.5);
			}
			.action-btn{
				display:flex;
				align-items: center;
				justify-content: center;
				width: 180upx;
				height: 100%;
				font-size: $font-base ;
				padding: 0;
				border-radius: 0;
				background: transparent;
			}
		}
	}
	
	.icon-more {
		font-size: 26upx;
		color: $font-color-light;
	}
	
	.zy-see-all {
		color: #fa436a;
	}
	.shop-section {
	  display: flex;
	  flex-direction: row;
	  align-items: center;
	  background: #fff;
	  margin-top: 16upx;
	  padding: 12upx 30upx;
	  
	  image {
	   width: 60upx;
	   height: 60upx;
	   border-radius: 60upx;
	  }
	  
	  .right {
	   color: $font-color-dark;
	   font-size: $font-base;
	   padding-left: 26upx;
	  }
	}
</style>
