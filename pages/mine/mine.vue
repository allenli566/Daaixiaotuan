<template>
	<view>
		<view class="loginWrapper" v-if="!hasLogin">
			<view class="logoBox">
				<image class="logoImg" src="../../static/logo.png" :lazy-load="true"></image>
				<view class="logoText">小团云购</view>
			</view>
			<button class="userInfoBtn" open-type="getUserInfo" @getuserinfo="getUserInfo" hover-class="navigator-hover">微信登录</button>
		</view>
		<view class="wrapper">
			<!-- 用户信息框 -->
			<view class="userBox">
				<view class="baseBox" hover-class="navigator-hover" @tap="userInfo.user_type == 0 ? toRegister() : toEditInfo()">
					<view>
						<image class="userHead" :src="userInfo.headimg" mode="aspectFill"></image>
						<view class="userMsg">
							<view class="userName">{{ userInfo.nickname ? userInfo.nickname : '暂无昵称' }}</view>
							<view class="userType">
								<text v-if="userInfo.user_type == 0">普通用户</text>
								<text v-else-if="userInfo.user_type == 1">代理</text>
								<text v-else-if="userInfo.user_type == 2">金牌代理</text>
								<text v-else-if="userInfo.user_type == 3">合伙人</text>
								<text v-else>未知</text>
							</view>
						</view>
					</view>
					<view>
						<text class="routerText" style="display: inline-block; vertical-align: middle; color: #666;" v-if="userInfo.user_status == 0">注册</text>
						<i class="iconfont routerIcon" style="display: inline-block; vertical-align: middle;">&#xe6a3;</i>
					</view>
				</view>
				<view class="bottomBox" hover-class="navigator-hover" @tap="toWallet()">
					<text>余额：￥{{ userInfo.balance_fee ? userInfo.balance_fee : 0 | toNewFixed }}</text>
					<text>金票：￥{{ userInfo.pre_fee ? userInfo.pre_fee : 0 | toNewFixed }}</text>
					<i class="iconfont">&#xe6a3;</i>
				</view>
			</view>

			<!-- 订单路由框 -->
			<view class="orderRouterBox">
				<view class="boxTop">
					<view class="title">我的订单</view>
					<!-- <view class="routerBtn">
            <text class="text">全部订单</text>
            <i class="iconfont routerIcon">&#xe6a3;</i>
          </view> -->
				</view>
				<view class="routerList">
					<view class="routerItem" hover-class="navigator-hover" @tap="toOrderList(0)">
						<i class="iconfont routerIcon">&#xe65e;</i>
						<text class="text">待发货</text>
					</view>
					<view class="routerItem" hover-class="navigator-hover" @tap="toOrderList(1)">
						<i class="iconfont routerIcon">&#xe778;</i>
						<text class="text">部分发货</text>
					</view>
					<view class="routerItem" hover-class="navigator-hover" @tap="toOrderList(2)">
						<i class="iconfont routerIcon">&#xe7f6;</i>
						<text class="text">已发货</text>
					</view>
					<!-- <view class="routerItem" hover-class="navigator-hover" @tap="toOrderList(3)">
            <i class="iconfont routerIcon">&#xe697;</i>
            <text class="text">发到虚拟仓</text>
          </view> -->
				</view>
			</view>

			<!-- 其他路由框 -->
			<view class="otherRouterBox">
				<view class="boxTop">
					<view class="title">我的工具</view>
					<view><view class="toMyShopBtn" v-if="userInfo.user_type != 0" @tap="toMyShop()">我的店铺 ></view></view>
				</view>
				<view class="routerList">
					<view class="line" style="margin-bottom: 20upx;">
						<!-- <navigator class="routerItem" url="fictitious/fictitious">
              <i class="iconfont routerIcon">&#xe676;</i>
              <text class="text">虚拟仓</text>
            </navigator> -->
						<navigator class="routerItem" url="address/address">
							<i class="iconfont routerIcon">&#xe651;</i>
							<text class="text">地址管理</text>
						</navigator>
						<navigator class="routerItem" url="bankCard/addBankCard">
							<i class="iconfont routerIcon">&#xe6ee;</i>
							<text class="text">银行卡</text>
						</navigator>
						<navigator class="routerItem" url="coupon/coupon">
							<i class="iconfont routerIcon">&#xe677;</i>
							<text class="text">优惠券</text>
						</navigator>
						<view class="routerItem" hover-class="navigator-hover" @tap="showQRcode()">
							<i class="iconfont routerIcon">&#xe6b0;</i>
							<text class="text">推广码</text>
						</view>
					</view>
					<view class="line" style="margin-bottom: 20upx;">
						<!-- <navigator class="routerItem" url="agoBuy">
              <i class="iconfont routerIcon">&#xe69d;</i>
              <text class="text">往期团购</text>
            </navigator> -->
						<!-- <navigator class="routerItem" url="bookSale">
              <i class="iconfont routerIcon">&#xe6b2;</i>
              <text class="text">预售商品</text>
            </navigator> -->
						<!-- <navigator class="routerItem" url="ranking/ranking">
              <i class="iconfont routerIcon">&#xe722;</i>
              <text class="text">排行榜</text>
            </navigator> -->
						<!-- <navigator class="routerItem" url="messagePage/messagePage">
							<i class="iconfont routerIcon">&#xe70a;</i>
							<text class="text">消息中心</text>
							<view class="point" v-if="msgCount > 0"></view>
						</navigator> -->
						<navigator class="routerItem" url="messagePage/messagePage">
							<i class="iconfont routerIcon">&#xe70a;</i>
							<text class="text">消息中心</text>
							<view class="point" v-if="msgCount > 0"></view>
						</navigator>
						<navigator class="routerItem" url="../index/brandList/brandList">
							<i class="iconfont routerIcon">&#xe713;</i>
							<text class="text">品牌街</text>
						</navigator>
						<navigator class="routerItem" url="../public/wechatPage">
							<i class="iconfont routerIcon">&#xe8bb;</i>
							<text class="text">加客服</text>
						</navigator>
						<navigator class="routerItem" url="../profit/profit">
							<i class="iconfont routerIcon">&#xe715;</i>
							<text class="text">收益</text>
						</navigator>
						<!-- <view class="routerItem"></view> -->
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import { mapState, mapMutations } from 'vuex';
const app = getApp();
export default {
	data() {
		return {
			imgUrls: app.globalData.imgUrls,
			userInfo: '', //用户基本信息
			msgCount: 0 //未读消息数量
		};
	},
	computed: {
		...mapState(['hasLogin'])
	},
	onShow() {
		this.getUserMsg(); //获取用户基本信息
	},
	filters: {
		toNewFixed: function(x) {
			return (x / 100).toFixed(2);
		}
	},
	methods: {
		...mapMutations(['login', 'logout']),
		/**
		 * @param {Object} data
		 * 用户授权
		 */
		getUserInfo(data) {
			const that = this;
			if (data.detail.userInfo) {
				uni.showLoading({
					title: '登录中'
				});
				console.log(data.detail.userInfo);

				let userInfo = data.detail.userInfo,
					nickName = userInfo.nickName,
					avatarUrl = userInfo.avatarUrl,
					gender = userInfo.gender,
					city = userInfo.city,
					country = userInfo.country,
					language = userInfo.language,
					province = userInfo.province;

				uni.setStorageSync('userInfo', userInfo);

				uni.login({
					success: login => {
						if (login.code) {
							uni.request({
								url: app.globalData.allUrls + 'api/oauth',
								header: app.globalData.header,
								data: {
									code: login.code,
									nickName: nickName,
									avatarUrl: avatarUrl,
									gender: gender,
									city: city,
									country: country,
									language: language,
									province: province
								},
								success: res => {
									console.log('%c 返回登录信息', 'color: green', res);
									if (res.data.code == 0) {
										let token = res.data.data.token;
										uni.showToast({
											title: '登录成功',
											icon: 'success'
										});
										setTimeout(() => {
											that.login(token); //执行vuex登录方法
											that.getUserMsg();
										}, 1000);
									}
								}
							});
						}
					}
				});
			}
		},
		/**
		 * 获取用户基本信息
		 */
		getUserMsg() {
			const that = this;
			let token = uni.getStorageSync('token') || '';

			uni.request({
				url: app.globalData.allUrls + 'api/getUserInfo',
				header: app.globalData.header,
				data: {
					version: app.globalData.version,
					token: token
				},
				success: res => {
					console.log('%c 获取用户基本信息', 'color: green', res);
					if (res.data.code === 0) {
						let userInfo = res.data.data.user;
						that.userInfo = userInfo;
						that.getMsgCount();
						// if (!userInfo.bind_phone) {
						//   uni.redirectTo({
						//     url: '../reg/reg'
						//   });
						// }
						if (userInfo.user_status == 0) {
							uni.redirectTo({
								url: '../reg/reg'
							});
						}
					} else {
						that.logout();
					}
				},
				fail: () => {
					that.logout();
				}
			});
		},
		/**
		 * 跳转到用户信息
		 */
		toEditInfo() {
			uni.navigateTo({
				url: 'userMsg/editUser'
			});
		},
		/**
		 * 跳转到注册页
		 */
		toRegister() {
			uni.navigateTo({
				url: '../public/register'
			});
		},
		/**
		 * 跳转到我的钱包
		 */
		toWallet() {
			uni.navigateTo({
				url: 'wallet/wallet'
			});
		},
		/**
		 * 跳转到我的订单
		 */
		toOrderList(index) {
			// uni.setStorageSync('tbIndex', index);
			uni.navigateTo({
				url: 'order/order?tbIndex=' + index
			});
		},
		/**
		 * 查看推广码图片
		 */
		showQRcode() {
			const that = this;
			let userInfo = that.userInfo;
			let imgUrls = that.imgUrls;

			let imgURL = imgUrls + userInfo.qrcode;

			console.log(imgURL);

			uni.previewImage({
				current: 0,
				urls: [imgURL]
			});
		},
		/**
		 * 查看证书
		 */
		showCertFile() {
			const that = this;
			let userInfo = that.userInfo;
			let imgUrls = that.imgUrls;

			let imgURL = imgUrls + userInfo.cert_file;

			console.log(imgURL);

			uni.previewImage({
				current: 0,
				urls: [imgURL]
			});
		},
		/**
		 * 获取未处理消息
		 */
		getMsgCount() {
			const that = this;
			let token = uni.getStorageSync('token');

			uni.request({
				url: app.globalData.allUrls + 'api/msgCount',
				header: app.globalData.header,
				data: {
					token: token
				},
				success: res => {
					console.log('%c 未读消息', 'color: green', res);
					if (res.data.code === 0) {
						that.msgCount = res.data.data.count;
						if (res.data.data.count != 0) {
							uni.showTabBarRedDot({
								index: 4
							});
						} else {
							uni.hideTabBarRedDot({
								index: 4
							});
						}
					} else {
						uni.hideTabBarRedDot({
							index: 4
						});
					}
				}
			});
		},
		/**
		 * 跳转到我的店铺
		 */
		toMyShop() {
			const that = this;
			let token = uni.getStorageSync('token') || '';

			uni.request({
				url: app.globalData.allUrls + 'api/visitMyShop',
				header: app.globalData.header,
				data: {
					token: token
				},
				success: res => {
					console.log('%c 访问自己的店铺', 'color: yellow', res);
					if (res.data.code === 0) {
						uni.setStorageSync('shop_id', res.data.data.shop_id);
						uni.switchTab({
							url: '../index/index'
						});
					} else {
						uni.showToast({
							title: '访问失败，请重试',
							icon: 'none'
						});
					}
				}
			});
		}
	}
};
</script>

<style lang="scss" scoped>
$themeColor: #cc33cc;

.loginWrapper {
	position: fixed;
	box-sizing: border-box;
	min-height: 100%;
	width: 100%;
	padding: 0 40upx;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	background-color: #fff;
	z-index: 10;

	.logoBox {
		margin-bottom: 400upx;

		.logoImg {
			display: inline-block;
			vertical-align: middle;
			width: 150upx;
			height: 150upx;
			margin-right: 40upx;
			border-radius: 20upx;
		}

		.logoText {
			display: inline-block;
			vertical-align: middle;
			font-size: 52upx;
			font-weight: bold;
			color: $themeColor;
		}
	}

	.userInfoBtn {
		width: 80%;
		font-size: 28upx;
		line-height: 80upx;
		height: 80upx;
		color: #fff;
		background-color: $themeColor;
		border-radius: 50upx;
	}

	.userInfoBtn:after {
		border: none;
	}
}

.wrapper {
	width: 100%;
	box-sizing: border-box;
	background-color: #f4f4f4;
	position: absolute;
	min-height: 100%;

	.userBox {
		padding: 30upx 30upx 0 30upx;
		background: -webkit-linear-gradient(#ffffff, #f4f4f4);
		/* Safari 5.1 - 6.0 */
		background: -o-linear-gradient(#ffffff, #f4f4f4);
		/* Opera 11.1 - 12.0 */
		background: -moz-linear-gradient(#ffffff, #f4f4f4);
		/* Firefox 3.6 - 15 */
		background: linear-gradient(#ffffff, #f4f4f4);

		/* 标准的语法 */
		.baseBox {
			display: flex;
			align-items: center;
			justify-content: space-between;
			margin-bottom: 30upx;

			.userHead {
				width: 160upx;
				height: 160upx;
				border-radius: 50%;
				display: inline-block;
				vertical-align: middle;
				margin-right: 30upx;
				background-color: #f1f1f1;
			}

			.userMsg {
				display: inline-block;
				vertical-align: middle;

				.userName {
					font-size: 34upx;
					font-weight: bold;
					padding-bottom: 30upx;
				}

				.userType {
					width: 150upx;
					height: 50upx;
					border-radius: 100upx;
					line-height: 50upx;
					text-align: center;
					background-color: $themeColor;
					color: #fff;
				}
			}

			.routerIcon {
				color: #999;
			}
		}

		.bottomBox {
			padding: 30upx;
			background: linear-gradient(to left, #dea96d 0%, #f6d59b 100%);
			border-top-left-radius: 20upx;
			border-top-right-radius: 20upx;
			display: flex;
			align-items: center;
			justify-content: space-between;
			color: #796335;
		}
	}

	.orderRouterBox {
		width: 100%;
		background: #fff;
		border-top: 1upx solid #eee;
		box-sizing: border-box;
		padding: 0 30upx 20upx 30upx;
		border-bottom: 1upx solid #eee;
		margin-bottom: 20upx;

		.boxTop {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 20upx 0;

			.title {
				font-size: 40upx;
				color: #999;
				font-weight: bold;
			}

			.routerBtn {
				color: #999;

				.text {
					display: inline-block;
					vertical-align: middle;
					margin-right: 10upx;
				}

				.routerIcon {
					display: inline-block;
					vertical-align: middle;
					font-size: 24upx;
				}
			}
		}

		.routerList {
			width: 100%;
			display: flex;
			align-items: center;
			justify-content: space-around;

			.routerItem {
				text-align: center;
				padding: 20upx 0;
				width: 25%;

				.routerIcon {
					font-size: 56upx;
					color: $themeColor;
					margin-bottom: 10upx;
				}

				.text {
					color: #666;
				}
			}
		}
	}

	.otherRouterBox {
		width: 100%;
		box-sizing: border-box;
		padding: 20upx;
		background: #fff;
		border-bottom: 1upx solid #eee;
		border-top: 1upx solid #eee;
		margin-bottom: 20upx;

		.boxTop {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 20upx 10upx;

			.title {
				font-size: 40upx;
				color: #999;
				font-weight: bold;
			}

			.toMyShopBtn {
				color: #666666;
			}
		}

		.line {
			display: flex;
			align-items: center;
			justify-content: space-around;

			.routerItem {
				text-align: center;
				padding: 20upx 0;
				width: 25%;
				position: relative;

				.routerIcon {
					font-size: 56upx;
					color: $themeColor;
					margin-bottom: 10upx;
				}

				.text {
					color: #666;
				}

				.point {
					position: absolute;
					background-color: red;
					width: 20upx;
					height: 20upx;
					border-radius: 50%;
					top: 20upx;
					right: 35upx;
					z-index: 5;
				}
			}
		}
	}
}
</style>
