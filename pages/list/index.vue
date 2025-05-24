<template>
  <view class="container">
    <view class="fixed-tabs">
      <u-tabs :list="items" :current="current" @change="onClickItem"></u-tabs>
    </view>
    <view class="scroll-content">
      <view class="productList">
        <view v-for="(item, index) in copyList" :key="item.id">
          <view class="item">
            <view class="left">
              <image
                class="img"
                :src="item.img"
                alt=""
              />
        
            </view>
            <view class="center">
              <view>{{ item.name }}</view>
              <view style="display: flex">
                <!-- <view style="margin-right: 40rpx;">编号：{{item.code}}</view> -->
                <view>库存：{{ item.number }}</view>
              </view>
              <view>
                <view>价格：{{ item.price }} 分</view>
                <!-- <view>{{item.count}}</view> --> 
              </view>
            </view>
            <view class="right">
              <view @click="pay(item, index)" style="color: cornflowerblue"
                >购买</view
              >
            </view>
          </view>
        </view>
        <view
          style="text-align: center; margin-top: 50%"
          v-if="copyList.length == 0"
          >暂无商品数据</view
        >
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      productList: [],
      count: 0,
      myProductList: [],
      messageList: [],
      typeList: [],
      items: [],
      current: 0,
      copyList: [],
    };
  },
  onShow() {
    this.getList();
    uni.getStorage({
      key: "count",
      success: (res) => {
        this.count = res.data;
      },
      fail: (err) => {
        console.log(err);
      },
    });
  },
  methods: {
    pay(item) {
      console.log(item);
      if (this.count > item.price) {
        // 积分变少 商品添加
        if (item.number < 1) {
          uni.showToast({
            title: "商品库存不足",
            icon: "none",
          });
          return;
        }
        this.count = this.count - item.price;
        // 判断item在 this.myProductList中是否存在 如果存在 num +1；
        this.productList.forEach((el) => {
          if (item.code == el.code) {
            el.number = el.number - 1;
          }
        });
        if (this.myProductList.find((el) => el.code == item.code)) {
          this.myProductList.forEach((el) => {
            if (el.code == item.code) {
              el.num += 1;
            }
          });
        } else {
          this.myProductList.push({
            ...item,
            num: 1, //数量
         
          });
        }
        this.messageList.unshift({
          title: "商品购买",
          content: `尊敬的用户您好，您已购买商品${item.name}`,
          type: "购买",
          time: `${new Date().Format("yyyy-MM-dd hh:mm:ss")}`,
        });
        uni.setStorage({
          key: "messageList",
          data: this.messageList,
        });
        uni.setStorage({
          key: "count",
          data: this.count,
        });
        uni.setStorage({
          key: "myProductList",
          data: this.myProductList,
        });
        uni.setStorage({
          key: "productList",
          data: this.productList,
        });
        uni.showToast({
          title: "购买成功",
          icon: "none",
        });
        // 添加提示
      } else {
        //
        uni.showToast({
          title: "积分不足",
          icon: "none",
        });
      }
    },
    getList() {
      uni.getStorage({
        key: "productList",
        success: (res) => {
          this.productList = res.data;
		  console.log(this.productList)
        },
        fail: function (err) {
          console.log(err);
        },
      });
      uni.getStorage({
        key: "myProductList",
        success: (res) => {
          this.myProductList = res.data;
        },
        fail: function (err) {
          console.log(err);
        },
      });
      uni.getStorage({
        key: "messageList",
        success: (res) => {
          this.messageList = res.data;
        },
        fail: function (err) {
          console.log(err);
        },
      });
      uni.getStorage({
        key: "typeList",
        success: (res) => {
          this.typeList = res.data;
          this.items = this.typeList.map((el, index) => {
            return {
              name: el.name,
              index: index,
            };
          });
          if (this.items.length > 0) {
            this.onClickItem1(this.items[0]);
          }
        },
        fail: function (err) {
          console.log(err);
        },
      });
    },
    onClickItem1(e) {
      let items = this.typeList.find((el) => el.name === e.name);
      this.current = e.index;
      this.copyList = this.productList.filter((el) => el.type === items.code);
	  console.log(this.copyList)
    },
    onClickItem(e) {
      let items = this.typeList.find((el) => el.name === e.name);
      this.current = e.index;
      this.copyList = this.productList.filter((el) => el.type === items.code);
    },
  },
};
</script>

<style lang="scss">
.container {
  position: relative;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

/* 固定 tabs 样式 */
.fixed-tabs {
  position: sticky;
  top: 0;
  z-index: 999;

  /* 可选：添加阴影效果 */
}

/* 滚动区域样式 */
.scroll-content {
  flex: 1;
  overflow-y: auto;
  padding-top: 80rpx;
  /* 预留 tabs 高度，根据实际调整 */
}

.productList {
  padding: 20rpx;

  .empty-tip {
    text-align: center;
    margin-top: 50%;
    color: #999;
  }

  .item {
    display: flex;
    background-color: #fff;
    padding: 20rpx;
    align-items: center;
    margin: 20rpx;
    border-radius: 4px;
    align-items: center;

    .left {
      .img {
	  margin-right: 30rpx;
        display: block;
        width: 120rpx;
        height: 120rpx;
      }
    }

    .center {
      flex: 1;
    }

    .right {
    }
  }
}
</style>
