<template>
  <div id="activityType">
    <el-card style="width: 95%;">
      <template #header>
        <div class="card-header">
          <el-row>
            <el-col :span="12">
              <span>活动分类</span>
            </el-col>
            <el-col :span="12" style="text-align: right;">
              <el-icon style="margin-right: 4px" :size="20" @click="clickTypeCard">
                <MoreFilled />
              </el-icon>
            </el-col>
          </el-row>
        </div>
      </template>
      <el-scrollbar height="400px">
        <el-row :gutter="24">
          <el-col
              v-for="(item) in typeList" :key="item.id"
              :span="3"
          ><div class="grid-content ep-bg-purple" />
            <el-card shadow="always" style="background-color: #feb47b" @click="clickTypeCard">
              <el-image :src="item.url" fit="cover" style="width: 100%; height: 100%;" />
              <div class="type">{{ item.typeName }}</div>
            </el-card>
          </el-col>
        </el-row>
      </el-scrollbar>
    </el-card>
  </div>
</template>

<script setup>
import {onMounted, ref} from "vue";
import {getActivityTypeList} from "@/api/home/home.js";
import {MoreFilled} from "@element-plus/icons-vue";
import router from "@/router/index.js";

// TODO: type图片展示
const typeList = ref([]);
onMounted(async () => {
  try {
    const res = await getActivityTypeList();
    if (res.data.label === 200) {
      typeList.value = res.data.data;
      // id: 1
      // typeName: "type1"
      // url: "url1"
    } else {
      console.error("Failed to fetch activity type list:", res.data.error);
    }
  } catch (error) {
    console.error("Panic to fetch activity type list:", error);
  }
});

const clickTypeCard = () => {
  router.push({name: 'ActivityQuery'})
}
</script>

<style>

#activityType {
  display: flex;
  justify-content: center;
}

el-row {
  margin-bottom: 20px;
}
el-row:last-child {
  margin-bottom: 0;
}
el-col {
  border-radius: 4px;
}

.type {
  display: flex;
  justify-content: center;
}

.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
</style>