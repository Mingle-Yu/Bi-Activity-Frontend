<template>
  <div id="college-ci">
    <el-form :model="formb" label-width="auto" style="max-width: 600px">
      <el-form-item label="学院头像：">
        <el-upload class="avatar-uploader" :disabled=!isEditable>
          <img v-if="formb.CollegeAvatarUrl" :src="formb.CollegeAvatarUrl" class="avatar" />
          <el-icon v-else class="avatar-uploader-icon">
            <Plus />
          </el-icon>
        </el-upload>
      </el-form-item>
      <el-form-item label="学院名称：">
        <el-input v-model="formb.CollegeName" disabled=true />
      </el-form-item>
      <el-form-item label="学院账号：">
        <el-input v-model="formb.CollegeAccount" disabled=true />
      </el-form-item>
      <el-form-item label="所属校区：">
        <el-select v-model="formb.Campus" placeholder="选择校区" size="large" style="width: 240px" :disabled=!isEditable>
          <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </el-form-item>
      <el-form-item label="详细地址：">
        <el-input v-model="formb.CollegeAddress" :disabled=!isEditable />
      </el-form-item>
      <el-form-item label="学院介绍：">
        <el-input v-model="formb.CollegeIntroduction" type="textarea" :disabled=!isEditable />
      </el-form-item>
      <el-form-item>
        <el-button @click="onEdit">编辑</el-button>
        <el-button @click="onSave">保存</el-button>
        <el-button @click="onCancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup>
// 导包
import { reactive, ref, onMounted } from 'vue'
import { ElMessage } from 'element-plus';
import { Plus } from '@element-plus/icons-vue';
import axios from 'axios';
import useCollegeStore from '/src/stores/college.js';

// 需要的变量
// 后端请求URL
const apiUrl = import.meta.env.VITE_COLLEGE1;
// jwt令牌
const collegeStore = useCollegeStore.data;
// 表单可编辑标记
const isEditable = ref(false);
// 用于展示的form表单数据
const formb = reactive({
    ID: '',
    CollegeAccount: '',
    CollegeName: '',
    Campus: '',
    CollegeAddress: '',
    CollegeIntroduction: '',
    CollegeAvatarUrl: ''
});
// 用于备份的form表单数据
const forma = reactive({
    ID: '',
    CollegeAccount: '',
    CollegeName: '',
    Campus: '',
    CollegeAddress: '',
    CollegeIntroduction: '',
    CollegeAvatarUrl: ''
});
// Selector的可取值
const options = [
  {
    value: 1,
    label: '珠海校区',
  },
  {
    value: 2,
    label: '深圳校区',
  },
  {
    value: 3,
    label: '广州校区',
  },
]

// 需要的方法
const fetchData = async () => {
  try {
    const response = await axios.get(`${apiUrl}/collegeInfo`, {
      params: {
        id: 1, 
      },
      headers: {
        'Content-Type': 'application/json',
        // 'Authorization': `Bearer ${collegeStore.token}`,
      },
    });
    if (response.status !== 200) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = response.data.data;
    Object.assign(formb, data);
    Object.assign(forma, data);
  } catch (error) {
    console.error('Failed to fetch data: ' + error.message);
  }
};

const updateData = async () => {
  try {
    // 数据预处理
    const flag = JSON.stringify(formb) === JSON.stringify(forma);
    // alert(flag);
    // 将 reactive 对象转换为普通对象，以便发送
    const dataToSend = JSON.parse(JSON.stringify(formb));
    // 发送 POST 请求到后端
    if (!flag) {
      const response = await axios.post(`${apiUrl}/collegeInfo`, dataToSend, {
        headers: {
          'Content-Type': 'application/json',
          // 如果需要的话，添加其他头部，比如认证令牌
          // 'Authorization': `Bearer ${yourAuthToken}`
        }
      });
      if (response.status !== 200) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
    }
  } catch (error) {
    console.error('Failed to update data: ' + error.message);
  }
};

onMounted(fetchData);

const onEdit = () => {
    isEditable.value = true;
}

const onSave = async () => {
    await updateData();
    await fetchData();
    isEditable.value = false;
    ElMessage({
      message: '已保存.',
      type: 'success',
    })
}

const onCancel = () => {
    Object.assign(formb, forma);
    isEditable.value = false;
    ElMessage({
      message: '已取消.',
      type: 'success',
    })
}
</script>

<style scoped>
.avatar-uploader .avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  text-align: center;
}

#college-ci {
  padding: 0;
}
</style>