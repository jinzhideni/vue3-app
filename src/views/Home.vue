<template>
  <div class="home-container">
    <div class="home-header" :class="{ active: state.headerScroll }">
      <router-link to="/category"
        ><van-icon name="bars" class="list-icon"
      /></router-link>
      <div class="search">
        <div class="title">黎宝商城</div>
        <i class="ico">|</i>
        <router-link class="title-search" to="./product-list?from=home"
          >山河无恙，人间皆安</router-link
        >
      </div>
      <router-link class="user" to="./login" v-if="state.isLogin"
        ><van-icon name="manager-o"
      /></router-link>
      <router-link class="user" to="/user" v-else>登录</router-link>
    </div>
    <div class="swper">
      <van-swipe class="my-swipe" :autoplay="3000" indicator-color="#1baeae">
        <van-swipe-item v-for="(item, index) in state.swperList" :key="index">
          <img
            class="swper-img"
            :src="item.carouselUrl"
            alt=""
            @click="goTo(item.redirectUrl)"
          />
        </van-swipe-item>
      </van-swipe>
    </div>
    <div class="category-list">
      <div
        class="category-item"
        v-for="item in state.categoryList"
        :key="item.categoryId"
        @click="tips"
      >
        <img class="category-img" :src="item.imgUrl" alt="" />
        <div class="sub-title">{{ item.name }}</div>
      </div>
    </div>
    <div class="new-goods">
      <div class="goods-header">新品上线</div>

      <div class="goods-item">
        <div
          class="item"
          v-for="item in state.newGoodsList"
          :key="item.goodsId"
          @click="goDetail(item)"
        >
          <img :src="$filters.prefix(item.goodsCoverImg)" class="item-img" />
          <div class="good-desc">
            <div class="title">{{ item.goodsName }}</div>
            <div class="price">¥ {{ item.sellingPrice }}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="new-goods">
      <div class="goods-header">热门商品</div>

      <div class="goods-item">
        <div
          class="item"
          v-for="item in state.hotsList"
          :key="item.goodsId"
          @click="goDetail(item)"
        >
          <img :src="$filters.prefix(item.goodsCoverImg)" class="item-img" />
          <div class="good-desc">
            <div class="title">{{ item.goodsName }}</div>
            <div class="price">¥ {{ item.sellingPrice }}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="new-goods">
      <div class="goods-header">最新推荐</div>

      <div class="goods-item">
        <div
          class="item"
          v-for="item in state.recommendList"
          :key="item.goodsId"
          @click="goDetail(item)"
        >
          <img :src="$filters.prefix(item.goodsCoverImg)" class="item-img" />
          <div class="good-desc">
            <div class="title">{{ item.goodsName }}</div>
            <div class="price">¥ {{ item.sellingPrice }}</div>
          </div>
        </div>
      </div>
    </div>
    <NavBar />
  </div>
</template>

<script setup>
import { reactive, onMounted, nextTick } from "vue";
import { useRouter } from "vue-router";
import NavBar from "@/components/NavBar.vue";
import { getHome } from "@/service/home";
import { useCartStore } from "@/stores/cart";
import { getLocal } from "@/common/js/utils";
import { showLoadingToast, closeToast, showToast } from "vant";

const router = useRouter();
const cart = useCartStore();
const state = reactive({
  headerScroll: true,
  isLogin: false, // 是否登录
  swperList: [], // 轮播图列表
  newGoodsList: [], // 新品上线列表
  hotsList: [], // 热门商品列表
  recommendList: [], // 最新推荐列表
  categoryList: [
    {
      name: "新蜂超市",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E8%B6%85%E5%B8%82%402x.png",
      categoryId: 100001,
    },
    {
      name: "新蜂服饰",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E6%9C%8D%E9%A5%B0%402x.png",
      categoryId: 100003,
    },
    {
      name: "全球购",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E5%85%A8%E7%90%83%E8%B4%AD%402x.png",
      categoryId: 100002,
    },
    {
      name: "新蜂生鲜",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E7%94%9F%E9%B2%9C%402x.png",
      categoryId: 100004,
    },
    {
      name: "新蜂到家",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E5%88%B0%E5%AE%B6%402x.png",
      categoryId: 100005,
    },
    {
      name: "充值缴费",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E5%85%85%E5%80%BC%402x.png",
      categoryId: 100006,
    },
    {
      name: "9.9元拼",
      imgUrl: "https://s.yezgea02.com/1604041127880/9.9%402x.png",
      categoryId: 100007,
    },
    {
      name: "领劵",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E9%A2%86%E5%88%B8%402x.png",
      categoryId: 100008,
    },
    {
      name: "省钱",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E7%9C%81%E9%92%B1%402x.png",
      categoryId: 100009,
    },
    {
      name: "全部",
      imgUrl:
        "https://s.yezgea02.com/1604041127880/%E5%85%A8%E9%83%A8%402x.png",
      categoryId: 100010,
    },
  ], // 详情图标
});

nextTick(() => {
  document.body.addEventListener("scroll", () => {
    let scrollTop =
      window.pageYOffset ||
      document.documentElement.scrollTop ||
      document.body.scrollTop;
    scrollTop > 100
      ? (state.headerScroll = true)
      : (state.headerScroll = false);
  });
});

onMounted(async () => {
  const token = getLocal("token");
  if (token) {
    state.isLogin = true;
    // 如果登录要更新购物车列表
    cart.updateCart();
  }
  showLoadingToast({ message: "加载中...", forbidClick: true });
  const { data } = await getHome();
  state.swperList = data.carousels;
  state.newGoodsList = data.newGoodses;
  state.hotsList = data.hotGoodses;
  state.recommendList = data.recommendGoodses;
  //console.log(data);
  closeToast();
});

const goDetail = (itemId) => {
  router.push({ path: `/product/${itemId}` });
};

const goTo = (url) => {
  window.open(url);
};

const tips = () => {
  showToast("敬请期待！");
};
</script>

<style lang="less" scoped>
.home-container {
  width: 100%;
  height: 100%;
  background: #f9f9f9;
  .home-header {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 50px;
    z-index: 999;
    padding: 8px;
    .list-icon {
      font-size: 18px;
      color: #1baeae;
    }
    .search {
      width: 80%;
      line-height: 20px;
      padding: 5px;
      display: inline-block;
      background: rgba(255, 255, 255, 0.7);
      border-radius: 200px;
      margin: auto 10px;
      .title {
        font-size: 20px;
        font-weight: bold;
        color: #1baeae;
        display: inline-block;
        margin-left: 20px;
      }
      .ico {
        font-size: 22px;
        margin-left: 10px;
      }
      .title-search {
        color: #2e2e2e;
        margin-left: 10px;
      }
    }
    .user {
      color: #1baeae;
      font-size: 16px;
      margin-left: 10px;
    }
    &.active {
      background: #1baeae;
      .user,
      .list-icon {
        color: #fff;
      }
    }
  }
  .swper {
    width: 100%;
    height: 180px;
    padding: 15px;
    position: relative;
    top: 28px;
    .my-swipe {
      width: 100%;
      height: 100%;
      .swper-img {
        width: 100%;
        height: 100%;
      }
    }
  }
  .category-list {
    width: 375px;
    display: flex;
    flex-shrink: 0;
    flex-wrap: wrap;
    background: #fff;
    .category-item {
      width: 20%;
      text-align: center;
      .category-img {
        width: 36px;
        height: 36px;
        margin: 13px auto 8px auto;
      }
    }
  }
  .new-goods {
    margin-top: 30px;
    .goods-header {
      width: 100%;
      height: 40px;
      padding: 10px;
      color: #1baeae;
      font-size: 20px;
      line-height: 20px;
      text-align: center;
    }

    .goods-item {
      display: flex;
      flex-wrap: wrap;
      justify-content: flex-start;
      .item {
        width: 50%;
        height: 100%;
        box-sizing: border-box;
        padding: 10px;
        border-bottom: 1px solid #e9e9e9;
        .item-img {
          width: 120px;
          height: 120px;
          margin: auto;
        }
        .good-desc {
          text-align: center;
          font-size: 14px;
          padding: 10px 0;
          .title {
            color: #222333;
          }
          .price {
            color: #1baeae;
          }
        }
        &:nth-child(2n + 1) {
          border-right: 1px solid#e9e9e9;
        }
      }
    }
  }
}
</style>
