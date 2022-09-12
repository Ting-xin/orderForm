<template>
  <el-popover placement="bottom-start" :width="200" trigger="click">
    <template #reference>
      <el-input v-model="state.label" style="width: 240px" readonly placeholder="选择类别" size="large" />
    </template>
    <el-tree :data="props.treeData" :props="props.defaultProps" @node-click="handleNodeClick" />
  </el-popover>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue'

export default defineComponent({
props: {
  modelValue: {
    type: String,
    default: '',
  },
  treeData: {
    type: Array,
    default: () => [],
  },
  defaultProps: {
    type: Object,
    default: () => {
      return {
        children: 'children',
        label: 'label',
      }
    },
  },
},
emits: ['update:modelValue', 'changeCategory'],
setup(props, { emit }) {
const state = reactive({
  label: '',
})

const handleNodeClick = (data: any) => {
  state.label = data.label
  emit('update:modelValue', data.lable)
  emit('changeCategory', data.label)
}
return {
reactive,
props,
state,
emit,
handleNodeClick,
};
},
});
</script>
