<template>
  <Form :visible="dialogInfo.isShow" @changeVisible="changeVisble" />
  <div class="common-layout">
    <el-container>
      <el-header class="header">
        <el-row class="center"><h2>DEAL 项目更新工具</h2></el-row>
        <el-row :gutter="20">
          <el-col :span="2" :offset="6">{{ type }}</el-col>
          <el-col :span="2">count: {{ count }}</el-col>
          <el-col :span="2" :offset="6">
            <el-button type="primary" size="large" @click="dialogInfo.isShow = true">Add</el-button>
          </el-col>
        </el-row>
      </el-header>
      <el-main class="center" style="margin-top: 20px">
        <el-table ref="initRef" row-key="id" :data="runningData" class="table">
          <el-table-column prop="ruleName" label="ruleName" width="100" />
          <el-table-column prop="description" label="description" width="400" />
          <el-table-column prop="type" label="type" width="100" />
          <el-table-column prop="operateCate" label="operateCate" width="100" />
          <el-table-column prop="count" label="count" width="100" />
          <el-table-column prop="state" label="state" width="100" />
          <el-table-column prop="operation" label="operation" width="100" />
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
import { ref, reactive } from 'vue'
import { ElTable } from 'element-plus'

const type = ref('type')
const count = ref(0)

interface Rule {
  id: string
  ruleName: string
  description: string
  state: string
  count: number
  operateCate: string
  type: string
  operation: string
}

const tableRef = ref<InstanceType<typeof ElTable>>()
const initRef = ref<InstanceType<typeof ElTable>>()
const runningRef = ref<InstanceType<typeof ElTable>>()
const pausedRef = ref<InstanceType<typeof ElTable>>()


const runningData: Rule[] = [
  {
    id: '1',
    ruleName: 'ruleName',
    description: 'description',
    state: 'state',
    count: 0,
    operateCate: 'cate',
    type: 'type',
    operation: 'operation',
  },
]

// 对话框
const dialogInfo = reactive({
  isShow: false,
})
function changeVisble() {
  dialogInfo.isShow = false;
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

.table{
  width: 1000px;
}
</style>
