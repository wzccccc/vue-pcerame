<template>
  <div class="search-dialog">
    <!-- <SvgIcon iconName="icon-search1" @click="handlerOpen"></SvgIcon> -->
    <i :class="'iconfont icon-search1'" @click="handlerOpen"></i>

    <el-dialog v-model="searchDialogVisible" width="300px" fullscreen destroy-on-close :modal="false" :show-close="false">
      <el-autocomplete
        ref="menuSearchRef"
        v-model="searchMenu"
        :fetch-suggestions="searchMenulist"
        :placeholder="$t(`layoutHeader.SearchTips`)"
        @select="handleClickMenu"
      >
        <template #prefix>
          <el-icon><Search /></el-icon>
        </template>
        <template #default="{ item }">
          <el-icon>
            <component :is="item.meta.icon"></component>
          </el-icon>
          <span>{{ $t(`layoutMenu.${item.meta.title}`) }}</span>
        </template>
      </el-autocomplete>
    </el-dialog>
  </div>
</template>

<script setup lang="ts" name="search">
import { MenuStore } from "@/store/modules/menu";

const menuStore = MenuStore();
const router = useRouter();

const searchDialogVisible = ref(false);
const searchMenu = ref("");
const menuSearchRef = ref();

// 数据
const menuList = computed(() => menuStore.flatDataMenuList.filter((item: { meta: { isHide: boolean } }) => !item.meta.isHide));

// 打开弹窗
const handlerOpen = () => {
  searchDialogVisible.value = true;
  nextTick(() => {
    setTimeout(() => {
      menuSearchRef.value.focus();
    });
  });
};

// 关闭弹窗
const handlerClose = () => {
  searchDialogVisible.value = false;
  searchMenu.value = "";
};

// 搜索菜单
const searchMenulist = (queryString: string, cb: Function) => {
  const results = queryString ? menuList.value.filter(createFilter(queryString)) : menuList.value;
  // call callback function to return suggestions
  cb(results);
};

// 筛选菜单
const createFilter = (queryString: string) => {
  return (restaurant: Menu.MenuOptions) => {
    return (
      restaurant.meta.title.toLowerCase().indexOf(queryString.toLowerCase()) > -1 ||
      restaurant.path.toLowerCase().indexOf(queryString.toLowerCase()) > -1
    );
  };
};

// 点击菜单
const handleClickMenu = (item: Menu.MenuOptions | Record<string, any>) => {
  if (item.meta.isLink) {
    window.open(item.meta.isLink, "_blank");
  } else {
    router.push(item.path);
  }
  handlerClose();
};
</script>

<style scoped lang="scss">
.search-dialog {
  height: 100%;
  :deep(.el-overlay-dialog) {
    background-color: rgb(0 0 0 / 50%) !important;
  }
  :deep(.el-dialog) {
    position: absolute;
    top: 10%;
    left: 50%;
    box-sizing: border-box;
    width: 30%;
    height: auto;
    transform: translateX(-50%);
    .el-dialog__header {
      display: none;
    }
    .el-dialog__body {
      padding: 0;
      .el-autocomplete {
        width: 100%;
      }
    }
  }
}
.el-autocomplete__popper {
  .el-icon {
    position: relative;
    top: 2px;
    font-size: 16px;
  }
  .el-autocomplete-suggestion__list {
    span {
      margin-left: 10px;
      font-size: 14px;
    }
  }
}
</style>
