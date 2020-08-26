<template>
	<view class="home">
		<view class="homeBtn">
			<image class="homeIcon" src="../../static/shop.png" mode=""></image>
		</view>
		<view class="apptitle">
			小团大购
		</view>
		<!-- 头部店铺栏 -->
		<view class="home-header">
			<view class="empty"></view>
			<view class="shopBox">
				<view class="box-left">
					<view class="shopImgbox">
						<image class="shopImg" src="../../static/default_photo.png" ></image>
						<image class="exchangeIcon" src="../../static/exchange.png" ></image>
					</view>
					<view class="shopMsg">
						<view class="shopName">约腾海小店</view>						
						<view class="shopDesc">云边开间小卖部</view>
					</view>
				</view>
				<view class="box-right">
					
				</view>
			</view>
		</view>
		<!-- 轮播图 -->
		<view class="banner-box">
			<swiper class="swiper" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
				<swiper-item v-for="(item, index) in swiperList" :key="index" @tap="toGoodsDetail(item.data)">
					<image class="swiper-item" :src="imgUrls + item.img"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- 秒杀 -->
		<view class="falsh-sale">
			<view class="falsh-title">
				<view class="falsh-desc">秒杀专区</view>				
				<view class="falsh-more" @tap="toSeckillList()">更多</view>
			</view>
			<view class="seckillList">
				<view class="emptyTip" v-if="flashList.length == 0">暂无秒杀商品</view>
				<scroll-view class="scroll-view_H" scroll-x="true" v-else-if="falshList.length > 0">
					<view class="scroll-view-item_H" v-for="(item, index) in falshList" :key="index" @tap="toSeckillList()">
						<image class="seckill_img" style="background-color: #f1f1f1;" mode="aspectFill" :src="imgUrls + item.imgsList[0]"></image>
						<view class="seckill_price">
							<text class="sale_price">￥{{ item.sale_price | toNewFixed }}</text>
							<text class="market_price">￥{{ item.market_price | toNewFixed }}</text>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
		<!-- 商品列表 -->
		<view class="goodsListBox">
			<view class="goods-title">
				<view class="title">
					热卖商品
				</view>
			</view>
			<view class="goods-list">
				<view class="goodsItem" v-for="(item, index) in goodsList" :key="index" @tap="toGoodsDetail(item.goods_id, item.sale_id)">
					<image class="goodsImg" :src="imgUrls + item.imgUrlsList[0]" mode=""></image>
					<view class="goodsMsgBox">
						<view class="goodsName">{{ item.goods_name }}</view>
						<view class="goodsDesc">{{ item.introduce }}</view>
						<view class="PriceBox">
							<view class="salePrice">
								￥<text>{{ item.sale_price | toNewFixed }}</text>
							</view>
							<view class="marketPrice">
								￥<text>{{ item.market_price | toNewFixed }}</text>
							</view>
						</view>
						<view class="goodsFreeStock">库存：{{ item.free_sale }}件</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp()

	export default {
		data() {
			return {
				imgUrls: app.globalData.imgUrls,
				swiperList: [],
				falshList: [],
				goodsList: []
			}
		},
		onLoad() {
			this.getBannerList();
			this.getFalshGoods();
			this.getGoodsList(1)
		},
		filters: {
			toNewFixed: function(x) {
				return (x / 100).toFixed(2);
			}
		},
		methods: {
			// 获取banner 
			getBannerList () {
				const that = this;
				uni.request({
					url: app.globalData.allUrls + 'api/getAds',
					header: app.globalData.header,
					success: res => {
						if(res.data.code === 0 && res.data.data) {
							this.swiperList = res.data.data;
						}
			
					}
				})
			},
			
			//获取秒杀数据
			getFalshGoods () {
				uni.request({
					url: app.globalData.allUrls + 'api/getFlashGoods',
					header: app.globalData.header,
					data: {
						falsh_time: 0
					},
					success: res => {
						if(res.data.code === 0 ) {
							let falshList = res.data.data.list;
	
							if(falshList.length > 0) {
								falshList.forEach(value => {
									value.imgsList = JSON.parse(value.img_array);
								})
							}
							this.falshList = falshList;
						}
					}
				})
			},
			//获取商品列表数据
			getGoodsList(pageNumber) {
				let token = uni.getStorageSync('token') || '';
				console.log(token)
				uni.request({
					url:app.globalData.allUrls + 'api/getGoodsList',
					header: app.globalData.header,
					data: {
						token: token,
						page: pageNumber
					},
					success: res => {
						if(res.data.code === 0) {
							console.log( res);
							if(res.data.data.list.length > 0) {
								
								res.data.data.list.map((item,index) => {
									item.imgUrlsList = JSON.parse(item.img_array); //转换图片地址
									item.price = this.Fen2Yuan(Number(item.sale_price)); //分转元
								});
								if(pageNumber > 1){
									res.data.data.list.map((item, index) => {
										that.goodsList.push(item);
										
									});
								}else{
									this.goodsList = res.data.data.list;
								}
							}
						}
					}
				})
			},
			//跳转到商品详情面
			toGoodsDetail(goods_id,sale_id) {
				uni.navigateTo({
					url: '../public/good_details?goods_id=' + goods_id
				})
			},
			
			Fen2Yuan(num) {
				if (typeof num !== 'number' || isNaN(num)) return null;
				return (num / 100).toFixed(2);
			},
			
			toSeckillList() {
				console.log(123)
				uni.navigateTo({
					url: 'seckillPage/seckillPage'
				});
			},
		}
	}
</script>

<style lang="scss" scoped>
	$themeColor:#cc33cc;
.home {
	background-color: #f7f7f7;
	width: 100%;
	position: absolute;
	min-height: 100%;
	.homeBtn {
		position: fixed;
		top: 100upx;
		left: 3%;
		width: 64upx;
		height: 64upx;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 10;
		background: rgba(255, 255, 255, 0.7);
		border-radius: 50%;
		box-shadow: 0upx 2upx 2upx #eee;
		
		.homeIcon{
			width: 40upx;
			height: 40upx;
			
		}
	}
	
	.apptitle{
		position: fixed;
		top: 100upx;
		width: 100%;
		height: 64upx;
		line-height: 64upx;
		opacity: 0.8;
		color: #000000;
		font-size: 34upx;
		text-align: center;
		font-weight: bold;
	}
	
	.home-header{
		background: linear-gradient(to right, #ec625c, $themeColor);
		
		.empty{
			width: 100%;
			height: 100upx;
		}
		
		.shopBox{
			height: 200upx;
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 0 30upx;
			.box-left {
				.shopImgbox {
					width: 120upx;
					height: 120upx;
					display: inline-block;
					vertical-align: middle;
					position: relative;
					margin-right: 20upx;
					.shopImg {
						width: 100%;
						height: 100%;
					}
					
					.exchangeIcon{
						position: absolute;
						width: 40upx;
						height: 40upx;
						bottom: 0;
						right: 0;
						border-radius: 50%;
						padding: 6upx;
						background-color: #FFFFFF;
						box-sizing: border-box;
					}
   				}
				
				.shopMsg {
					display: inline-block;
					vertical-align: middle;
					color: #ffffff;
					.shopName {
						width: 320upx;
						font-size: 32upx;
						font-weight: bold;
						white-space: nowrap;
						overflow: hidden;
						text-overflow: ellipsis;
					}
					
					.shopDesc {
						// color: #ffffff;
						width: 320upx;
						white-space: nowrap;
						overflow: hidden;
						text-overflow: ellipsis;
					}
				}
			}
		}
	}

	.banner-box{
		box-sizing: border-box;
		width: 100%;
		height: 380upx;
		margin-bottom: 20upx;
		margin-top: 10upx;
		.swiper{
			box-sizing: border-box;
			height: 380upx;
			width: 100%;
			border-radius: 20upx;
			.swiper-item{
				width: 100%;
				height: 100%;
				border-radius: 20upx;
				transform: scale(0.95);
				
			}
		}
	}
	
	.falsh-sale{
		background: #fff;
		border-radius: 20upx;
		margin: 20upx;
		.falsh-title {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 20upx;
			
			.falsh-desc {
				font-size: 34upx;
				color: #666;
				font-weight: bold;
			}
			
			.falsh-more {
				color: #999;
			}
		}
		
		.seckillList {
			.emptyTip {
				width: 100%;
				text-align: center;
				font-size: 34upx;
				color: #999;
				font-weight: bold;
				padding: 30upx 0;
			}
			.scroll-view_H {
				white-space: nowrap;
		
				.scroll-view-item_H {
					display: inline-block;
					margin-right: 20upx;
					.seckill_img {
						width: 100%;
						height: 180upx;
						border-radius: 20upx;
						display: block;
					}
					.seckill_price {
						width: 180upx;
						display: flex;
						align-items: center;
						justify-content: center;
						flex-direction: column;
						padding: 20upx 0;
						.sale_price {
							font-weight: bold;
							font-size: 32upx;
							color: #d43334;
							margin-bottom: 10upx;
						}
						.market_price {
							color: #999;
							text-decoration: line-through;
							font-size: 24upx;
						}
					}
				}
		
				.scroll-view-item_H:first-child {
					margin-left: 20upx;
				}
			}
		}
		
	
	
	}
	
	.goodsListBox{
		margin: 20upx;
		border-radius: 20upx;
		.goods-title{
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 20upx;
			.title {
				font-size: 34upx;
				color: #666;
				font-weight: bold;
			}
			
		}
		.goods-list{
			.goodsItem {
			
				float: left;
				width: 49%;
				overflow: hidden;
				margin-right: 2%;
				margin-bottom: 20upx;
				background-color: #ffffff;
				box-shadow: 0upx 5upx 15upx rgba(139, 156, 188, 0.5);
				border-radius: 20upx;
				
				
			
				.goodsImg {
					width: 100%;
					height: 250upx;
					background-color: #f1f1f1;
				}	
				.goodsMsgBox{
					width: 100%;
					box-sizing: border-box;
					padding: 20upx;
					.goodsName {
						width: 100%;
						font-size: 32upx;
						margin-bottom: 10upx;
						overflow: hidden;
						text-overflow: ellipsis;
						white-space: nowrap;
						
					}
					.goodsDesc{
						width: 100%;
						font-size: 32upx;
						margin-bottom: 10upx;
						overflow: hidden;
						text-overflow: ellipsis;
						white-space: nowrap;
					}
					.PriceBox{
						width: 100%;
						margin: 20upx 0 0 0;
						.salePrice{
							display: inline-block;
							vertical-align: middle;
							font-weight: bold;
							font-size: 24upx;
							color: #d43334;
							margin-right: 10upx;
							text{
								font-size: 40upx;
							}
						}
						
						.marketPrice{
							display: inline-block;
							vertical-align: middle;
							color: #999;
							text-decoration: line-through;
							font-size: 24upx;
						}
					}
					.goodsFreeStock{
						color: #999999;
					}
				}
				
			}
			.goodsItem:nth-child(2n) {
				margin-right: 0;
			}
		}

	}



}
</style>
