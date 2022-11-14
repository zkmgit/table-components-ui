<template>
  <div class="basic-layout">
    <!-- 筛选项插槽 -->
    <div>
      <slot name="basicForm" />
    </div>
    <div v-loading="tableRegionLoading" class="table-region">
      <div class="table-button">
        <!-- 顶部按钮插槽 -->
        <slot name="tableButton" />

        <div class="fixed-right-button">
          <el-button size="mini" type="primary" @click="queryData">{{ queryBtnName }}</el-button>
          <el-button size="mini" @click="resetQueryParams">{{ resetBtnName }}</el-button>
        </div>
      </div>

      <div class="basic-table">
        <el-table stripe border :style="{ height: `${tableHeight}px` }" :height="tableHeight" :data="tableData" @selection-change="handleSelectionChange">
          <template v-for="(item, index) in tableColumns">
            <el-table-column v-if="selection && index == 0" :key="index + 'selection'" fixed="left" type="selection" width="60" />

            <!-- 插槽 -->
            <el-table-column v-if="item.slot" :key="item.slot" :prop="item.prop" :label="item.label" :min-width="item.width" :fixed="item.fixed">
              <!-- 表头当作html渲染 -->
              <template v-if="item.slotHeader" slot="header">
                <slot :name="item.slotHeader"><span v-html="item.label" /></slot>
              </template>

              <template slot-scope="scope">
                <slot :name="item.slot" :row="scope.row" :index="scope.$index" />
              </template>
            </el-table-column>

            <!-- 正常展示 -->
            <el-table-column v-else :key="`${item.prop}-${item.label}`" :prop="item.prop" :label="item.label" :min-width="item.width" :fixed="item.fixed" />
          </template>
        </el-table>
        <pagination ref="page" :total="total" :limit="pageParam.ps" :page="pageParam.pn" @pagination="paginationChange" />
      </div>
    </div>
  </div>
</template>
<script>
import { Pagination } from '@/components/Pagination'
import { isFunction, isArray } from 'lodash'

export default {
  name: 'BasicTable',
  components: { Pagination },
  props: {
    selection: { type: Boolean, default: false },
    tableHeight: { type: Number, default: 500 },
    queryParams: {
      type: Object,
      default: () => {
        return {}
      }
    },
    tableColumns: { type: Array, default: () => [] },
    apiFn: {
      type: Function,
      default: () => {
        return () => {}
      }
    },
    isAfterFetch: { type: [Function, undefined], default: undefined },
    isBeforeFetch: { type: [Function, undefined], default: undefined },
    isImmediateQuery: { type: Boolean, default: true },
    queryBtnName: { type: String, default: '查询' },
    resetBtnName: { type: String, default: '重置' }
  },
  data() {
    return {
      tableRegionLoading: false,
      tableData: [],
      tableSelectList: [],
      total: 0,
      pageParam: {
        ps: 10,
        pn: 1
      }
    }
  },
  watch: {
    apiFn: {
      handler() {
        this.queryData()
      },
      deep: true
    }
  },
  beforeMount() {
    this.isImmediateQuery && this.queryData()
  },
  methods: {
    handleSelectionChange(val) {
      this.tableSelectList = val
    },
    async queryData() {
      this.tableRegionLoading = true

      let params = {
        ...this.queryParams,
        ...this.pageParam
      }

      if (isFunction(this.isBeforeFetch)) {
        params = this.isBeforeFetch(params)
      }

      try {
        const apiResult = await this.apiFn(params)

        if (apiResult && apiResult.code === '1') {
          if (isArray(apiResult.result) && apiResult.result.length > 0) {
            this.tableData = isFunction(this.isAfterFetch) ? this.isAfterFetch(apiResult.result) : apiResult.result
          } else {
            this.tableData = []
          }

          this.total = apiResult.total
        } else {
          if (apiResult && Reflect.has(apiResult, 'msg')) {
            this.$message.error(apiResult.msg)
          }
        }
      } catch {
        this.tableData = []
        this.total = 0
      }

      this.tableRegionLoading = false
    },
    resetQueryParams() {
      Object.assign(this.queryParams, this.$parent.$options.data().queryParams)
      Object.assign(this.$data.pageParam, this.$options.data().pageParam)
      this.queryData()
    },
    paginationChange(param) {
      this.pageParam.ps = param.limit
      this.pageParam.pn = param.page
      this.queryData()
    }
  }
}
</script>
<style lang="scss" scoped>
.basic-layout {
  .table-region {
    margin: 10px 10px 0 10px;
    max-width: 100%;
    border-radius: 4px;
    border: 1px solid #e6ebf5;
    background-color: #ffffff;
    overflow: hidden;
    color: #303133;
    transition: 0.3s;

    .table-button {
      height: 40px;
      line-height: 40px;
      margin-left: 5px;

      .fixed-right-button {
        float: right;
        margin-right: 5px;
      }
    }

    .basic-table {
      margin-left: 5px;
      margin-right: 5px;
    }
  }
}
</style>
