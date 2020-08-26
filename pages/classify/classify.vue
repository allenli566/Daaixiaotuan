<template>
  <view class="wrapper">
    <!-- 左侧分类框 -->
    <view class="typeListBox" @touchmove.stop="preventTouchMove()">
      <view class="typeItem" v-for="(item, index) in typeList" :class="activeIndex == item.group_id ? 'active' : ''" :key="index" @tap="chooseType(item.group_id)">
        {{ item.group_name }}
      </view>
    </view>
    <!-- 右侧商品框 -->
    <view class="goodsBox">
      <view v-if="goodsList.length == 0" class="tipBox">暂无数据</view>
      <!-- 视频资源列表 -->
      <view class="goodsList" v-if="goodsList.length > 0">
        <view class="goodsItem" v-for="(item, index) in goodsList" :key="index" hover-class="navigator-hover" @tap="toGoodsDetail(item.goods_id)">
          <image class="goodsImg" mode="aspectFill" :src="imgUrls + item.imgUrlsList[0]"></image>
          <view class="goodsName">{{ item.goods_name }}</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
const app = getApp();
export default {
  data() {
    return {
      imgUrls: app.globalData.imgUrls,
      typeList: [],
      pageNumber: 1,
      pageCount: 1,
      activeIndex: 0,
      goodsList: []
    };
  },
  onLoad() {
    this.getgoodsGroups();
  },
  onPullDownRefresh() {
    this.getGoodsList(1, this.activeIndex);

    setTimeout(function() {
      uni.stopPullDownRefresh();
    }, 500);
  },
  onReachBottom() {
    const that = this;
    let pageNumber = that.pageNumber;
    let pageCount = that.pageCount;

    if (pageNumber < pageCount) {
      pageNumber = pageNumber + 1;
      that.getGoodsList(pageNumber, this.activeIndex);
    }
  },
  methods: {
    /**
     * 获取商品分组
     */
    getgoodsGroups() {
      const that = this;
      let typeList = [
        {
          group_id: '0',
          group_name: '全部'
        }
      ];

      uni.request({
        url: app.globalData.allUrls + 'api/getgoodsGroups',
        header: app.globalData.header,
        success: res => {
          console.log('%c 商品分组', 'color: green', res);
          if (res.data.data) {
            res.data.data.forEach(value => {
              typeList.push(value);
            });
          }
        },
        complete: () => {
          that.getGoodsList(1, 0);
        }
      });

      that.typeList = typeList;
    },
    /**
     * @param {Object} page
     * @param {Object} group_type
     * 获取商品列表
     */
    getGoodsList(page, group_type) {
      const that = this;
      let token = uni.getStorageSync('token') || '';
      let goodsList = [];

      uni.showLoading({
        title: '加载中...'
      });

      uni.request({
        url: app.globalData.allUrls + 'api/getGoodsList',
        header: app.globalData.header,
        data: {
          token: token,
          page: page,
          group_type: group_type
        },
        success: res => {
          console.log('%c 商品列表', 'color: green', res);
          if (res.data.code === 0) {
            that.pageCount = res.data.data.page_count;
            if (res.data.data.list.length > 0) {
              goodsList = res.data.data.list;

              goodsList.map((item, index) => {
                item.imgUrlsList = JSON.parse(item.img_array); //转换图片地址
              });

              if (page > 1) {
                goodsList.map(value => {
                  that.goodsList.push(value);
                });
              } else {
                that.goodsList = goodsList;
              }
            } else {
              that.goodsList = [];
            }
          }
        },
        complete: () => {
          uni.hideLoading();
        }
      });
    },
    /**
     * @param {Object} group_id
     * 类型点击事件
     */
    chooseType(group_id) {
      this.activeIndex = group_id;
      this.getGoodsList(1, group_id);
      //重置页面高度
      uni.pageScrollTo({
        scrollTop: 0,
        duration: 200
      });
    },
    /**
     * @param {Object} goods_id
     * 跳转到商品详情
     */
    toGoodsDetail(goods_id) {
      uni.navigateTo({
        url: '../public/goodsDetail?goods_id=' + goods_id
      });
    },
    preventTouchMove: function() {
      //此处为空函数即可，用于阻止用户对模态框外的任何操作
    }
  }
};
</script>

<style lang="scss" scoped>
$themeColor: #cc33cc;
.wrapper {
  width: 100%;
  font-size: 28upx;
  .typeListBox {
    position: fixed;
    height: 100%;
    width: 160upx;
    border-right: 1upx solid #eee;
    overflow: auto;
    .typeItem {
      width: 100%;
      height: 90upx;
      line-height: 90upx;
      border-bottom: 1upx solid #eee;
      text-align: center;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    .active {
      background: $themeColor;
      color: #fff;
    }
  }
  .goodsBox {
    padding-left: 180upx;
    .tipBox {
      color: #666;
      width: 100%;
      box-sizing: border-box;
      text-align: center;
      display: block;
      margin: 0 auto;
      padding-top: 500upx;
    }
    .goodsList {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      .goodsItem {
        width: 30%;
        height: 260upx;
        margin-bottom: 18upx;
        margin-right: 18upx;
        &:nth-child(3n) {
          margin-right: 0;
        }
        .goodsImg {
          width: 100%;
          height: 180upx;
          background: #f1f1f1;
        }
        .goodsName {
          text-align: center;
        }
      }
    }
  }
}
</style>
