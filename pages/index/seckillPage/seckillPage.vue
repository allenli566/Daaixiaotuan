<template>
	<view class="wrapper">
		
		<view style="height: 80upx;">
		  <view class="tabBox" v-if="tabList.length > 0">
		    <QSTabs :current="activeIndex" :tabs="tabList" minWidth="187.5" activeColor="#cc33cc" @change="change($event)" :autoCenter="true" />
		  </view>
		</view>
		
		
		<view class="goodsListBox">
		  <view class="tipBox" v-if="goodsList.length == 0">
		    <image class="tipImg" src="../../../static/cartList.png"></image>
		    <view class="tip">暂无秒杀商品</view>
		  </view>
		  <view class="goodsList" v-if="goodsList.length > 0">
		    <view class="goodsItem" v-for="(item, index) in goodsList" :key="index">
		      <image class="goodsImg" :src="imgUrls + item.imgUrlsList[0]" mode="aspectFill"></image>
		      <view class="goodsMsgBox">
		        <view class="goodsName">{{ item.goods_name }}</view>
		        <view class="goodsDesc">{{ item.introduce }}</view>
		        <view class="goodsHandleBox">
		          <view class="priceBox">
		            <view class="salePrice">
		              ￥
		              <text>{{ item.sale_price | toNewFixed }}</text>
		            </view>
		            <view class="marketPrice">￥{{ item.market_price | toNewFixed }}</view>
		          </view>
		          <view class="buyButton" v-if="item.is_flash == 1" hover-class="navigator-hover" @tap="toGoodsDetail(item.goods_id)">立即抢购</view>
		        </view>
		      </view>
		    </view>
		  </view>
		</view>
	</view>
</template>

<script>
import QSTabs from '@/components/QS-tabs/QS-tabs.vue';
const app = getApp();	

export default {
	components: {
	  QSTabs
	},
	data() {
	  return {
	    imgUrls: app.globalData.imgUrls,
	    tabList: [],
	    activeIndex: 0,
	    goodsList: [],
	    pageNumber: 1,
	    pageCount: 1
	  };
	},
	filters: {
	  toNewFixed: function(x) {
	    return (x / 100).toFixed(2);
	  }
	},
	onLoad() {
	  this.getFlashTimes();
	  
	},
	methods:{
		getFlashTimes() {
		  const that = this;
		  let tabList = [];
		
		  uni.request({
		    url: app.globalData.allUrls + 'api/getFlashTimes',
		    header: app.globalData.header,
		    success: res => {
		     
		      if (res.data.code === 0 && res.data.data) {
		        res.data.data.forEach((value, index) => {
		          tabList.push(value.flash_name);
		          if (value.is_flash == 1) {
		            that.activeIndex = index;
		          }
		        });
		        that.tabList = tabList;
		        this.getGoodsList()
		      }
		    }
		  });
		},
		
		
		getGoodsList() {
			let goodsList = []
			uni.request({
				url: app.globalData.allUrls + 'api/getFlashGoods',
				header: app.globalData.header,
				
				
				success: res => {
					
					if(res.data.code === 0) {
						if(res.data.data.list.length > 0) {
							goodsList = res.data.data.list;
							goodsList.map((item, index) => {
							  item.imgUrlsList = JSON.parse(item.img_array); //转换图片地址
							});
							this.goodsList = goodsList;
							
						}
					}
				}
			})
		}
	}
}
</script>

<style lang="scss" scoped>
	$themeColor: #cc33cc;
	.wrapper{
		width: 100%;
		font-size: 28upx;
		.tabBox {
		  width: 100%;
		  position: fixed;
		  top: 0;
		  left: 0;
		  background-color: #fff;
		}
		.goodsListBox {
		  border-radius: 20upx;
		  background-color: #fff;
		  // box-shadow: 0upx 16upx 40upx rgba(139, 156, 188, 0.15);
		  .tipBox {
		    box-sizing: border-box;
		    width: 100%;
		    text-align: center;
		    padding: 250upx 0;
		    .tipImg {
		      width: 200upx;
		      height: 200upx;
		      margin-bottom: 40upx;
		    }
		    .tip {
		      color: #666;
		    }
		  }
		  .goodsList {
		    .goodsItem {
		      box-sizing: border-box;
		      width: 100%;
		      height: 280upx;
		      display: flex;
		      align-items: center;
		      border-bottom: 1upx solid #eee;
		      padding: 20upx;
		      .goodsImg {
		        width: 35%;
		        height: 100%;
		        background-color: #f1f1f1;
		      }
		      .goodsMsgBox {
		        width: 65%;
		        padding-left: 20upx;
		        .goodsName {
		          font-size: 32upx;
		          margin-bottom: 10upx;
		        }
		        .goodsDesc {
		          color: #999;
		        }
		        .goodsHandleBox {
		          display: flex;
		          align-items: center;
		          justify-content: space-between;
		          margin-top: 50upx;
		          .priceBox {
		            .salePrice {
		              font-weight: bold;
		              font-size: 24upx;
		              color: #d43334;
		              margin-bottom: 5upx;
		              text {
		                font-size: 36upx;
		              }
		            }
		            .marketPrice {
		              color: #999;
		              text-decoration: line-through;
		              font-size: 24upx;
		            }
		          }
		          .buyButton {
		            width: 200upx;
		            height: 60upx;
		            border-radius: 40upx;
		            color: #fff;
		            background: linear-gradient(to right, #ec625c, $themeColor);
		            text-align: center;
		            line-height: 60upx;
		          }
		        }
		      }
		    }
		  }
		}
	}
</style>
