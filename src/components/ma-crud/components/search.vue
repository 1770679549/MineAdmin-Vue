<!--
 - MineAdmin is committed to providing solutions for quickly building web applications
 - Please view the LICENSE file that was distributed with this source code,
 - For the full copyright and license information.
 - Thank you very much for using MineAdmin.
 -
 - @Author X.Mo<root@imoi.cn>
 - @Link   https://gitee.com/xmo/mineadmin-vue
-->
<template>
  <a-spin :loading="searchLoading" tip="加载数据中..." v-if="isShowSearch">
    <a-form
      :model="searchForm"
      layout="inline"
      :label-align="props.searchLabelAlign"
      ref="search"
      @submit="handlerSearch"
    >
      <div :class="`w-full grid grid-cols-1 lg:grid-cols-${ props.searchCustomerLayout ? 1 : 4 }`">
        <template v-if="! props.searchCustomerLayout">
          <template v-for="(item, index) in columns" :key="index">
            <a-form-item
              :field="item.dataIndex"
              :label="item.title"
              :label-col-style="{ width: item.searchLabelWidth || props.searchLabelWidth }"
            >
              <slot :name="`${item.dataIndex}`" v-bind="{ searchForm, item }">
                <a-select
                  v-if="['select', 'radio', 'checkbox', 'transfer'].includes(item.formType)"
                  v-model="searchForm[item.dataIndex]"
                  :virtual-list-props="item.virtualList ? { height: 200 } : undefined"
                  :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                  allow-clear
                  allow-search
                  :max-tag-count="1"
                  :options="formDictData[item.dataIndex]"
                  :multiple="item.multiple || ['transfer', 'checkbox'].includes(item.formType)"
                  @change="handlerCascader($event, item)"
                />

                <a-cascader
                  v-else-if="item.formType === 'cascader'"
                  v-model="searchForm[item.dataIndex]"
                  :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                  allow-clear
                  allow-search
                  :disabled="item.disabled"
                  :readonly="item.readonly"
                  :expand-trigger="item.trigger || 'click'"
                  :options="formDictData[item.dataIndex]"
                  :multiple="item.multiple"
                />

                <a-tree-select
                  v-else-if="item.formType === 'treeSelect' || item.formType === 'tree-select'"
                  v-model="searchForm[item.dataIndex]"
                  :treeProps="{ virtualListProps: item.virtualList ? { height: 240 } : undefined }"
                  :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                  :disabled="item.disabled"
                  :readonly="item.readonly"
                  allow-clear
                  allow-search
                  :field-names="item.dict.props || { key: 'value', title: 'label' }"
                  :tree-checkable="item.multiple"
                  :multiple="item.multiple"
                  :data="formDictData[item.dataIndex]"
                />

                <component
                  v-else-if="['date', 'month', 'year', 'week', 'quarter', 'range', 'time'].includes(item.formType)"
                  :is="getComponent(item)"
                  v-model="searchForm[item.dataIndex]"
                  :placeholder="
                    item.formType === 'range'
                    ? ['请选择开始时间', '请选择结束时间']
                    : item.searchPlaceholder ? item.searchPlaceholder : `请选择${item.title}`
                  "
                  :show-time="item.showTime"
                  :format="item.format || ''"
                  :mode="item.mode"
                  allow-clear
                  style="width: 100%;"
                />

                <component
                  v-else
                  :is="getComponent(item)"
                  v-model="searchForm[item.dataIndex]"
                  :placeholder="item.searchPlaceholder ? item.searchPlaceholder : `请输入${item.title}`"
                  allow-clear
                />
              </slot>
            </a-form-item>
          </template>
        </template>
        <a-row v-else>
          <template v-for="(item, index) in columns" :key="index">
            <a-col :span="props.searchCustomerLayout ? item.searchSpan || 24 : 24">
              <a-form-item
                :field="item.dataIndex"
                :label="item.title"
                :label-col-style="{ width: item.searchLabelWidth || props.searchLabelWidth }"
              >
                <slot :name="`${item.dataIndex}`" v-bind="{ searchForm, item }">
                  <a-select
                    v-if="['select', 'radio', 'checkbox', 'transfer'].includes(item.formType)"
                    v-model="searchForm[item.dataIndex]"
                    :virtual-list-props="item.virtualList ? { height: 200 } : undefined"
                    :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                    allow-clear
                    allow-search
                    :max-tag-count="1"
                    :options="formDictData[item.dataIndex]"
                    :multiple="item.multiple || ['transfer', 'checkbox'].includes(item.formType)"
                    @change="handlerCascader($event, item)"
                  />

                  <a-cascader
                    v-else-if="item.formType === 'cascader'"
                    v-model="searchForm[item.dataIndex]"
                    :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                    allow-clear
                    allow-search
                    :disabled="item.disabled"
                    :readonly="item.readonly"
                    :expand-trigger="item.trigger || 'click'"
                    :options="formDictData[item.dataIndex]"
                    :multiple="item.multiple"
                  />

                  <a-tree-select
                    v-else-if="item.formType === 'treeSelect' || item.formType === 'tree-select'"
                    v-model="searchForm[item.dataIndex]"
                    :treeProps="{ virtualListProps: item.virtualList ? { height: 240 } : undefined }"
                    :placeholder="item.searchPlaceholder || `请选择${item.title}`"
                    :disabled="item.disabled"
                    :readonly="item.readonly"
                    allow-clear
                    allow-search
                    :field-names="item.dict.props || { key: 'value', title: 'label' }"
                    :tree-checkable="item.multiple"
                    :multiple="item.multiple"
                    :data="formDictData[item.dataIndex]"
                  />

                  <component
                    v-else-if="['date', 'month', 'year', 'week', 'quarter', 'range', 'time'].includes(item.formType)"
                    :is="getComponent(item)"
                    v-model="searchForm[item.dataIndex]"
                    :placeholder="
                      item.formType === 'range'
                      ? ['请选择开始时间', '请选择结束时间']
                      : item.searchPlaceholder ? item.searchPlaceholder : `请选择${item.title}`
                    "
                    :show-time="item.showTime"
                    :format="item.format || ''"
                    :mode="item.mode"
                    allow-clear
                    style="width: 100%;"
                  />

                  <component
                    v-else
                    :is="getComponent(item)"
                    v-model="searchForm[item.dataIndex]"
                    :placeholder="item.searchPlaceholder ? item.searchPlaceholder : `请输入${item.title}`"
                    allow-clear
                  />
                </slot>
              </a-form-item>
            </a-col>
          </template>
        </a-row>
      </div>
      <div class="text-center mt-5 w-full">
        <a-space size="medium">
          <a-button type="primary" html-type="submit"><icon-search /> 提交</a-button>
          <a-button @click="reset"><icon-delete /> 清空</a-button>
          <slot name="searchButtons" />
        </a-space>
      </div>
    </a-form>
  </a-spin>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { request } from '@/utils/request'
import { isFunction, isArray, get } from 'lodash'
import { Message } from '@arco-design/web-vue'
import commonApi from '@/api/common'
import { handlerProps } from '../js/common'

const searchLoading = ref(true)
const formDictData = ref({})
const isShowSearch = ref(false)
const search = ref(null)
const columns = ref([])

let searchForm = reactive({})

const emits = defineEmits(['search'])

watch(() => props.columns, () => {
  init()
}, { deep: true })

const requestDict = (url, method, params, data, timeout = 10 * 1000) => request({ url, method, params, data, timeout })

const init = async () => {
  if (props.columns.length > 0) {
    columns.value = props.columns.filter( item => item.search === true )
  }

  const allowRequestFormType = ['radio', 'checkbox', 'select', 'transfer', 'treeSelect', 'tree-select', 'cascader']
  const allowCoverFormType = ['radio', 'checkbox', 'select', 'transfer']
  if (props.columns.length > 0) {
    isShowSearch.value = columns.value.length > 0 ? true : false
    props.columns.map(async item => {
      if (item.search || item.dict) {
        if (item.dataIndex && item.search) {
          searchForm[item.dataIndex] = item.searchDefaultValue || undefined
        }

        if (allowRequestFormType.includes(item.formType) && item.dict) {
          if (item.dict.name) {
            const response = await commonApi.getDict(item.dict.name)
            if (response.data) {
              formDictData.value[item.dataIndex] = handlerProps(allowCoverFormType, item, response.data)
            }
          } else if (item.dict.url) {
            const response = await requestDict(item.dict.url, item.dict.method || 'GET', item.dict.params || {}, item.dict.body || {})
            if (response.data) {
              formDictData.value[item.dataIndex] = handlerProps(allowCoverFormType, item, response.data)
            }
          } else if (item.dict.data) {
            if (isArray(item.dict.data)) {
              formDictData.value[item.dataIndex] = handlerProps(allowCoverFormType, item, item.dict.data)
            } else if (isFunction(item.dict.data)) {
              const response = await item.dict.data()
              formDictData.value[item.dataIndex] = handlerProps(allowCoverFormType, item, response)
            }
          }
        }
      }
    })
  }
  searchLoading.value = false
}

const getComponent = (item => {
  if (! item.formType) {
    return `a-input`
  }
  if (['input', 'password', 'textarea'].includes(item.formType)) {
    return 'a-input'
  } else if (['date', 'month', 'year', 'week', 'quarter', 'range', 'time'].includes(item.formType)) {
    return `a-${item.formType}-picker`
  } else {
    return `a-input`
  }
})

const handlerSearch = () => {
  emits('search', searchForm)
}

const reset = () => {
  search.value.resetFields()
  emits('search', searchForm)
}

// 处理联动
const handlerCascader = (val, column) => {
  if (column.cascaderItem && isArray(column.cascaderItem) && column.cascaderItem.length > 0 && val) {
    searchLoading.value = true
    column.cascaderItem.map(async name => {
      const dict = columns.value.filter(col => col.dataIndex === name && col.dict )[0].dict
      if (dict && dict.url && dict.props) {
        let response
        if (dict && dict.url.indexOf('{{key}}') > 0) {
          response = await requestDict(dict.url.replace('{{key}}', val), dict.method || 'GET', dict.params || {}, dict.data || {})
        } else {
          let temp = { key: val }
          const params = Object.assign(dict.params || {}, temp)
          const data   = Object.assign(dict.data || {}, temp)
          response = await requestDict(dict.url, dict.method || 'GET', params || {}, data || {})
        }
        if (response.data && response.code === 200) {
          formDictData.value[name] = response.data.map(dicItem => {
            return {
              'label': dicItem[ (dict.props && dict.props.label) || 'code'  ],
              'value': dicItem[ (dict.props && dict.props.value) || 'value' ]
            } 
          })
        } else {
          Message.error('字典联动请求失败：' + name)
          console.error(response)
        }
      }
    })
    searchLoading.value = false
  }
}

const props = defineProps({
  columns: { type: Array },
  searchLabelWidth: { type: String, default: () => 'auto' },
  searchLabelAlign: { type: String, default: () => 'right' },
  searchCustomerLayout: { type: Boolean },
})

const dictTrans = (dataIndex, value) => {
  if (formDictData.value[dataIndex] && formDictData.value[dataIndex].tran) {
    return formDictData.value[dataIndex].tran[value]
  } else {
    return value
  }
}

const dictColors = (dataIndex, value) => {
  if (formDictData.value[dataIndex] && formDictData.value[dataIndex].colors) {
    return formDictData.value[dataIndex].colors[value]
  } else {
    return undefined
  }
}

init()

defineExpose({ dictColors, dictTrans, searchForm })
</script>

<style scoped lang="less">
:deep(.arco-form-item-label-required-symbol svg) {
  vertical-align: baseline !important;
}
</style>
