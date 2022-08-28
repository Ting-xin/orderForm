<template>
  <Form :visible="dialogInfo.isShow" @changeVisible="changeVisble" @init="init" />
  <div class="common-layout">
    <el-container>
      <el-header class="header">
        <el-row class="center"><h2>DEAL Update Tool</h2></el-row>
        <el-row :gutter="20">
          <el-col :span="2" :offset="6">
            <el-select v-model="type" class="m-2" placeholder="Select"> <el-option v-for="item in ['Init', 'Ongoing', 'Paused']" :key="item" :label="item" :value="item" /> </el-select
          ></el-col>
          <el-col :span="4">TotalCount: {{ count }}</el-col>
          <el-col :span="2" :offset="6">
            <el-button type="primary" size="large" @click="dialogInfo.isShow = true">Add</el-button>
          </el-col>
        </el-row>
      </el-header>
      <el-main class="center" style="margin-top: 20px">
        <el-table ref="tableRef" row-key="id" :data="filterData" class="table" stripe>
          <el-table-column prop="ruleName" label="ruleName" width="200" show-overflow-tooltip="true" />
          <el-table-column prop="description" label="description" width="400" show-overflow-tooltip="true" />
          <el-table-column prop="type" label="type" width="100">
            <template #default="scope">
              <el-tag>{{ scope.row.type }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="operationCate" label="operationCate" width="200" />
          <el-table-column prop="count" label="count" width="100" />
          <el-table-column prop="state" label="state" width="150">
            <template #default="scope">
              <el-tag v-if="type === 'Ongoing'">{{ scope.row.state }}</el-tag>
              <el-tag v-else type="warning">{{ scope.row.state }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column label="Operations" width="150">
            <template #default="scope">
              <el-button v-if="type === 'Init' || type === 'Paused'" size="small" type="primary" @click="handelEnable(scope.$index, scope.row)">Start</el-button>
              <el-button v-else-if="type === 'Ongoing'" size="small" type="warning" @click="handlePaused(scope.$index, scope.row)">Pause</el-button>
              <!-- <el-button v-else-if="type === 'Paused'" size="small" type="primary" @click="handelEnable(scope.$index, scope.row)">Enable</el-button> -->
              <el-button v-if="type === 'Init' || type === 'Paused'" size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">Delete</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-main>
    </el-container>
  </div>
</template>

<script lang="ts">
export default {
  name: 'Main',
}
</script>

<script lang="ts" setup>
import Form from './Form.vue'
import { ref, reactive, computed } from 'vue'
import { ElMessage, ElTable } from 'element-plus'

const type = ref('Init')
const count = ref(0)

interface Rule {
  rid: string
  ruleName: string
  description: string
  state: string
  count: number
  operationCate: string
  type: string
}

const tableRef = ref<InstanceType<typeof ElTable>>()
let data = reactive({
  init: [],
  ongoing: [],
  paused: [],
})
const filterData = computed(() => data[type.value.toLocaleLowerCase()])

const init = async () => {
  await fetch('/api/rule', {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  })
    .then((response) => response.json())
    .then((res) => {
      if (res.code === 0) {
        data.init = res.data.init
        data.ongoing = res.data.ongoing
        data.paused = res.data.paused
      } else {
        ElMessage({
          message: '获取失败',
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

  await fetch('/api/rule/total', {
    method: 'GET',
  })
    .then((res) => res.json())
    .then((res) => {
      count.value = res.data
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

const handleDelete = (index: number, row: Rule) => {
  fetch('/api/rule/' + row.rid, {
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
const handelEnable = (index: number, row: Rule) => {
  fetch('/api/rule/' + row.rid + '/5000', {
    method: 'GET',
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
const handlePaused = (index: number, row: Rule) => {
  fetch('/api/rule/' + row.rid, {
    method: 'GET',
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

// 对话框
const dialogInfo = reactive({
  isShow: false,
})
function changeVisble() {
  dialogInfo.isShow = false
}
</script>

<style scoped>
.center {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.header {
  height: 100px;
}
.float-left {
  float: left;
}
.float-right {
  float: right;
}

.table {
  width: 1300px;
}
</style>
