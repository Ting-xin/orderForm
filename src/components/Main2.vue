<template>
  <div class="common-layout">
    <el-container>
      <el-header class="header">
        <el-row class="center"><h2>DEAL Update Tool</h2></el-row>
        <el-row :gutter="20">
          <el-col :span="6" :offset="6">
            <el-switch v-model="value" size="large" active-text="新增规则" inactive-text="新增内容" @click="change" style="--el-switch-on-color: #135b4b; --el-switch-off-color: #1b538d" />
          </el-col>
        </el-row>
      </el-header>
      <el-main class="center" style="margin-top: 20px">
        <div v-if="isContent">
          <Content />
        </div>
        <div v-else>
          <Rule />
        </div>
      </el-main>
    </el-container>
  </div>
</template>

<script lang="ts">
export default {
  name: 'Main2',
}
</script>

<script lang="ts" setup>
import { computed } from '@vue/reactivity'
import { ref } from 'vue'
import Content from './Content.vue'
import Rule from './Rule.vue'

const value = ref('Content')
let isContent = ref(true);
const change = () => {
  isContent.value = !isContent.value
}
console.log(isContent)
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
</style>
