<template>
	<view class="wrapper">
		<view class="bannerBox">
			<swiper class="swiper" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
				<swiper-item >
					<image class="swiperImg" :src="imgUrls + goodsDetail.imgUrlsList[0]"  ></image>
				</swiper-item>

			</swiper>
		</view>
		
		<view class="goodsMsgBox">
			<view class="mainBox">
				<view class="goodsTitleBox">
					<view class="goodsTitle">{{ goodsDetail.goods_name }}</view>
					<!-- <view class="goodsBrand" v-if="goodsDetail.brand_id != 0" hover-class="navigator-hover" @tap="toBrand(goodsDetail.brand_id)">{{ goodsDetail.brand_name }}</view> -->
					<view style="clear: both;"></view>
				</view>
				<view class="goodsDesc">{{ goodsDetail.introduce }}</view>
				<view class="goodsPrice">
					团购价
					<text class="text">￥</text>
					<text class="price">{{ goodsDetail.sale_price | toNewFixed }}</text>
				</view>
			</view>
		
			<view class="besideBox">
				<view class="besideItem">
					<text class="itemText">市场价：</text>
					<text class="itemContent">￥{{ goodsDetail.market_price | toNewFixed }}</text>
				</view>
				<view class="besideItem">
					<text class="itemText">邮费标准：</text>
					<text class="itemContent">新疆/西藏/青海/内蒙 +10元，其余地区包邮</text>
				</view>
				<view class="besideItem">
					<text class="itemText"></text>
					<text class="itemContent">{{ goodsDetail.description }}</text>
				</view>
			</view>
		</view>
		<!-- 商品详情 -->
		<view class="goodsImagesBox">
			<view class="title">商品详情</view>
			<view class="videobox" v-if="videoUrl"><video id="goodsVideo" :src="imgUrls + videoUrl"></video></view>
			<image class="image" mode="widthFix" v-for="(item, index) in goodsDetail.imgUrlsList" :key="index" :src="imgUrls + item"></image>
		</view>
		
		<!-- 底部按扭 -->
		<view style="height: 100upx;">
			<view class="handleBox">
				<view class="handleBtn cartBtn">
					<!-- <button class="button" hover-class="navigator-hover" @tap="toCart()"><i class="iconfont btnIcon">&#xe6af;</i></button> -->
					<button class="button" hover-class="navigator-hover" @tap="toHome()"><i class="iconfont btnIcon">&#xe6b8;</i></button>
				</view>
				<view class="handleBtn shareBtn">
					<button class="button" hover-class="navigator-hover" v-if="!hasLogin" open-type="getUserInfo" @getuserinfo="getUserInfo"><i class="iconfont btnIcon">&#xe6f3;</i></button>
					<button class="button" hover-class="navigator-hover" v-else @tap="openSharePopup()"><i class="iconfont btnIcon">&#xe6f3;</i></button>
				</view>
				<view class="handleBtn buyBtn">
					<button class="button" v-if="!hasLogin" open-type="getUserInfo" hover-class="navigator-hover" @getuserinfo="getUserInfo">购买</button>
					<button class="button" v-else hover-class="navigator-hover" @tap="openPopup()">购买</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		components: {
			
		},
		data () {
			return {
				imgUrls: app.globalData.imgUrls, //图片地址
				goodsDetail: '', //商品详情,
			}
		},
		filters: {
			toNewFixed: function(x) {
				return (x / 100).toFixed(2);
			}
		},
		onLoad(Option) {
			this.getGoodsDetail(Option.goods_id)
		},
		methods: {
			getGoodsDetail(goods_id) {
				let token = uni.getStorageSync('token') || '';
				
				uni.request({
					url: app.globalData.allUrls + 'api/getGoods',
					header: app.globalData.header,
					data: {
						token: token,
						goods_id: goods_id
					},
					success: res => {
						if(res.data.code === 0) {
							let goodsDetail = res.data.data;
							goodsDetail.price = this.Fen2Yuan(Number(goodsDetail.sale_price));
							goodsDetail.imgUrlsList = JSON.parse(goodsDetail.img_array);
							goodsDetail.attr.map(value => {
								value.selectIndex = 0;
							});
							
							this.goodsDetail = goodsDetail
							console.log(this.goodsDetail)
			
						}
						
						
					}
				})
			},
			
			
			//分转元
			Fen2Yuan(num) {
				if (typeof num !== 'number' || isNaN(num)) return null;
				return (num / 100).toFixed(2);
			},
		}
		
	}
</script>

<style lang="scss" scoped>
	$themeColor: #cc33cc;
.wrapper{
	width: 100%;
	box-sizing: border-box;
	background-color: #f1f1f1;
	position: absolute;
	min-height: 100%;
	
	.bannerBox{
		width: 100%;
		height: 550upx;
		.swiper {
			width: 100%;
			height: 550upx;
			.swiperImg {
				width: 100%;
				height: 100%;
			}
		}
	}
	
	.goodsMsgBox {
		box-sizing: border-box;
		padding: 50upx 30upx 20upx 30upx;
		background-color: #fff;
		border-top: 1upx solid #eee;
		border-bottom: 1upx solid #eee;
		margin-bottom: 10upx;
		.mainBox {
			margin-bottom: 30upx;
			.goodsTitleBox {
				.goodsTitle {
					float: left;
					font-size: 40upx;
					font-weight: bold;
					margin-bottom: 20upx;
				}
				.goodsBrand {
					float: right;
					box-sizing: border-box;
					padding: 10upx 20upx;
					// border: 1upx solid $themeColor;
					border-radius: 30upx;
					// color: $themeColor;
					color: #fff;
					background: #ec625c;
				}
			}
	
			.goodsDesc {
				color: #999;
				margin-bottom: 30upx;
			}
			.goodsPrice {
				.text {
					color: #d43334;
					font-weight: bold;
				}
				.price {
					color: #d43334;
					font-size: 46upx;
					font-weight: bold;
				}
			}
		}
		.besideBox {
			.besideItem {
				box-sizing: border-box;
				padding: 20upx 0;
				border-bottom: 1upx solid #eee;
				.itemText {
					color: #666;
				}
				.itemContent {
					color: #999;
				}
				.brandName {
					text-decoration: underline;
					color: $themeColor;
				}
			}
		}
	}
	
	.goodsImagesBox {
		background-color: #fff;
		box-sizing: border-box;
		border-top: 1upx solid #eee;
		margin-bottom: 10upx;
		.title {
			text-align: center;
			font-size: 40upx;
			color: #999;
			font-weight: bold;
			padding: 30upx 0 40upx 0;
		}
		.videobox {
			box-sizing: border-box;
			margin-bottom: 10px;
			width: 100%;
			#goodsVideo {
				width: 100%;
			}
		}
		.image {
			width: 100%;
			display: block;
			margin-bottom: 10upx;
		}
	}
	
	.handleBox {
		position: fixed;
		width: 100%;
		box-sizing: border-box;
		left: 0;
		bottom: 0;
		height: 100upx;
		background: #fff;
		border-top: 1upx solid #eee;
	
		.handleBtn {
			height: 100upx;
			border: none;
			display: inline-block;
			vertical-align: middle;
			border-radius: 0%;
			font-size: 28upx;
		}
		.cartBtn,
		.shareBtn {
			width: 15%;
			color: #333;
			text-align: center;
			border-left: 1upx solid #eee;
			background: #fff;
			box-sizing: border-box;
			.button {
				padding: 0;
				background: #fff;
				font-size: 28upx;
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;
				height: 100upx;
				line-height: 100upx;
			}
			.button:after {
				border: none;
			}
			.btnIcon {
				color: $themeColor;
				margin-bottom: 5upx;
				display: block;
				font-size: 48upx;
			}
			.text {
				font-size: 24upx;
				color: #666;
			}
		}
		.buyBtn {
			// background: $themeColor;
			// width: 40%;
			background: linear-gradient(to right, #ec625c, $themeColor);
			width: 70%;
	
			.button {
				width: 100%;
				background: linear-gradient(to right, #ec625c, $themeColor);
				color: #fff;
				text-align: center;
				line-height: 100upx;
				font-size: 28upx;
			}
			.button:after {
				border: none;
			}
		}
	}
	
	
}
</style>
