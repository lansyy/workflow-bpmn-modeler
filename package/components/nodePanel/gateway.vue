<template>
  <div>
    <x-form ref="xForm" v-model="formData" :config="formConfig">
      <template #executionListener>
        <el-badge :value="executionListenerLength">
          <el-button size="small" @click="dialogName = 'executionListenerDialog'">编辑</el-button>
        </el-badge>
      </template>
    </x-form>
    <executionListenerDialog
      v-if="dialogName === 'executionListenerDialog'"
      :element="element"
      :modeler="modeler"
      @close="finishExecutionListener"
    />
  </div>
</template>

<script>
import mixinPanel from '../../common/mixinPanel'
import mixinExecutionListener from '../../common/mixinExecutionListener'
export default {
  mixins: [mixinPanel, mixinExecutionListener],
  data() {
    return {
      formData: {}
    }
  },
  computed: {
    formConfig() {
      return {
        inline: false,
        item: [
          {
            xType: 'input',
            name: 'id',
            label: '节点 id',
            rules: [{ required: true, message: 'Id 不能为空' }]
          },
          {
            xType: 'input',
            name: 'name',
            label: '节点名称'
          },
          {
            xType: 'colorPicker',
            name: 'color',
            label: '节点颜色'
          },
          {
            xType: 'slot',
            name: 'executionListener',
            label: '执行监听器'
          },
          {
            xType: 'switch',
            name: 'async',
            label: '异步',
            activeText: '是',
            inactiveText: '否'
          }
        ]
      }
    }
  },
  watch: {
    'formData.async': function(val) {
      if (val === '') val = null
      this.updateProperties({ 'flowable:async': val })
    }
  },
  created() {
    const cache = {
      ...this.element.businessObject,
      ...this.element.businessObject.$attrs
    }
    // 移除flowable前缀，格式化数组
    for (const key in cache) {
      if (key.indexOf('flowable:') === 0) {
        const newKey = key.replace('flowable:', '')
        cache[newKey] = cache[key]
        delete cache[key]
      }
    }
    this.formData = cache
  }
}
</script>

<style>

</style>
