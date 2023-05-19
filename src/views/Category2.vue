<template>
  <div class="category">
    <div class="header">
      <van-icon name="arrow-left" class="icon" @click="backHome" />
      <van-search
        class="search"
        v-model="value"
        placeholder="全场50起步"
        @search="onSearch"
      />
    </div>
    <div class="category-left">
      <ul class="nav-side">
        <li v-for="item in state.list" :key="item.categoryId"></li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import { useRouter } from "vue-router";
import { getCategory } from "@/service/good";
import { showLoadingToast, closeToast } from "vant";

const router = useRouter();
// 点击左箭头返回home页
const backHome = () => {
  router.push({ name: "home" });
};
const value = ref("");
const onSearch = (v) => {
  console.log(v);
};

return {
  backHome,
  value,
  onSearch,
};

const state = reactive({
  list: [],
});

onMounted(async () => {
  showLoadingToast("加载中...");
  const { data } = await getCategory();
  closeToast();
  state.list = data;
  console.log(state.list);
});
</script>

<style lang="less" scoped>
.category {
  .header {
    margin: 5px 8px;
    display: flex;
    align-items: center;
    display: flex;
    .icon {
      font-size: 18px;
    }
    .search {
      flex: 1;
    }
  }
}
</style>
