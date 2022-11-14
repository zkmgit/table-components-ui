<template>
  <div>
    <basic-table ref="basicTableRef" :table-columns="tableColumns" :query-params="queryParams" :selection="true" :api-fn="apiFn">
      <template #basicForm>
        <!-- 表单筛选项 -->
        <el-input></el-input>
        <el-select></el-select>
      </template>

      <template #action>
        <!-- 操作按钮 -->
        <el-button size="mini" type="warning">编辑</el-button>
        <el-button size="mini" type="danger">删除</el-button>
      </template>

      <template #tableButton>
        <!-- 功能按钮 -->
        <el-button size="mini" type="primary">新增</el-button>
        <el-button size="mini" type="success">导出</el-button>
      </template>
    </basic-table>
  </div>
</template>

<script>
import { BasicTable } from './components/Table'

export default {
  name: 'App',
  components: {
    BasicTable
  },
  data() {
    return {
      // 筛选项入参对象
      queryParams: {},
      // 模拟表格分页查询接口返回数据
      apiFn: () => {
        return {
          code: '1',
          result: [
            { id: 1, username: 'aa', password: '123456' },
            { id: 2, username: 'bb', password: '123456' },
            { id: 3, username: 'cc', password: '123456' },
            { id: 4, username: 'dd', password: '123456' },
            { id: 5, username: 'ee', password: '123456' }
          ],
          total: 5
        }
      },
      // 表头配置数据
      tableColumns: [
        { label: 'ID', prop: 'id', width: 80 },
        { label: '用户名', prop: 'username', width: 100 },
        { label: '密码', prop: 'password', width: 90 },
        { label: '操作', slot: 'action', width: 90 }
      ]
    }
  }
}
</script>
