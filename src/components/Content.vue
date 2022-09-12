<template>
  <el-row class="center">
    <el-col :span="8" class="center">
      <span style="width: 200px; margin-right: 20px">文本:</span>
      <span style="width: 600px; margin-left: 0px; padding-left: 0px">
        <el-input v-model="text" placeholder="请填写文本" size="large" style="margin-left: 0px; padding-left: 0px" />
      </span>
    </el-col>
    <el-col :span="6" :offset="1">
      <span style="width: 200px; margin-right: 20px">选择位置:</span>
      <span style="width: 200px">
        <el-select v-model="location" class="m-6" placeholder="Select" size="large" style="width: 180px">
          <el-option v-for="item in ['A', 'B', 'C', 'D', 'E', 'F']" :key="item" :label="'自然文本' + item" :value="item" />
        </el-select>
      </span>
    </el-col>
    <el-col :span="2" :offset="1">
      <el-button type="primary" size="large" @click="add">确定</el-button>
    </el-col>
  </el-row>
  <el-divider />
  <el-table ref="tableRef" row-key="id" :data="data.data" class="table" stripe>
    <el-table-column prop="text" label="内容" width="400" show-overflow-tooltip="true" align="center" />
    <el-table-column prop="location" label="位置" width="250" show-overflow-tooltip="true" align="center" />
    <el-table-column prop="state" label="状态" width="200" align="center">
      <template #default="scope">
        <el-tag v-if="scope.state === 'Ongoing'">{{ scope.row.state }}</el-tag>
        <el-tag v-else state="warning">{{ scope.row.state }}</el-tag>
      </template>
    </el-table-column>
    <el-table-column label="操作" width="300" align="center">
      <template #default="scope">
        <span v-if="(scope.row.useCount === 0 && scope.row.state === 'INIT') || scope.row.state === 'PAUSED'">
          <el-button v-if="scope.row.state === 'INIT' || scope.row.state === 'PAUSED'" type="primary" @click="handelEnable(scope.$index, scope.row)">开始</el-button>
          <el-button v-else-if="scope.row.state === 'ONGOING'" type="warning" @click="handlePaused(scope.$index, scope.row)">暂停</el-button>
        </span>
        <span v-else>
          <el-button v-if="scope.row.state === 'INIT' || scope.row.state === 'PAUSED'" type="primary" @click="handelEnable(scope.$index, scope.row)" style="width: 130px">开始</el-button>
          <el-button v-else-if="scope.row.state === 'ONGOING'" type="warning" @click="handlePaused(scope.$index, scope.row)" style="width: 130px">暂停</el-button>
        </span>
        <el-button
          style="margin-left: 10px"
          v-if="(scope.row.useCount === 0 && scope.row.state === 'INIT') || scope.row.state === 'PAUSED'"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)"
          >删除</el-button
        >
      </template>
    </el-table-column>
  </el-table>
</template>

<script lang="ts" setup>
import Form from './Form.vue'
import { ref, reactive, computed, defineComponent } from 'vue'
import { ElMessage, ElTable } from 'element-plus'

const count = ref(0)

interface Content {
  cid: number
  text: string
  location: string
  state: string
}
const tableRef = ref<InstanceType<typeof ElTable>>()
let data = reactive({ data: [] })

const init = async () => {
  await fetch('/api/content', {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  })
    .then((response) => response.json())
    .then((res) => {
      if (res.code === 0) {
        data.data = res.data
      } else {
        ElMessage({
          message: '获取内容失败',
          type: 'warning',
        })
      }
    })
    .catch((err) => {
      ElMessage({
        message: '服务器错误: ' + err,
        type: 'error',
      })
    })
}
init.bind(this)
init()

const text = ref('')
const location = ref('A')
const add = () => {
  fetch('/api/content', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      text: text.value,
      location: location.value,
    }),
  })
    .then((res) => {
      if (res.status === 200) {
        ElMessage({
          message: '添加成功',
          type: 'success',
        })
        init()
      } else {
        ElMessage({
          message: '添加失败',
          type: 'warning',
        })
      }
    })
    .catch((err) => {
      ElMessage({
        message: '服务器错误: ' + err,
        type: 'error',
      })
    })
}

const handleDelete = (index: number, row: Content) => {
  fetch('/api/content/' + row.cid, {
    method: 'DELETE',
  })
    .then((res) => {
      if (res.status === 200) {
        ElMessage({
          message: '删除成功',
          type: 'success',
        })
        init()
      } else {
        ElMessage({
          message: '删除失败',
          type: 'warning',
        })
      }
    })
    .catch((err) => {
      ElMessage({
        message: '服务器错误: ' + err,
        type: 'error',
      })
    })
}
const handelEnable = (index: number, row: Content) => {
  fetch('/api/content/sa/' + row.cid, {
    method: 'PUT',
  })
    .then((res) => {
      if (res.status === 200) {
        ElMessage({
          message: '启动成功',
          type: 'success',
        })
        init()
      } else {
        ElMessage({
          message: '启动失败',
          type: 'warning',
        })
      }
    })
    .catch((err) => {
      ElMessage({
        message: '服务器错误: ' + err,
        type: 'error',
      })
    })
}
const handlePaused = (index: number, row: Content) => {
  fetch('/api/content/pa/' + row.cid, {
    method: 'PUT',
  })
    .then((res) => {
      if (res.status === 200) {
        ElMessage({
          message: '停止成功',
          type: 'success',
        })
        init()
      } else {
        ElMessage({
          message: '停止失败',
          type: 'warning',
        })
      }
    })
    .catch((err) => {
      ElMessage({
        message: '服务器错误: ' + err,
        type: 'error',
      })
    })
}
</script>

<style scoped>
.center {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
