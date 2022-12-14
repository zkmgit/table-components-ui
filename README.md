# Author-Kay.
# BasicTable 基于Element UI 二次封装的表格分页组件

## 基本使用方法
```js
<template>
  <div>
    <basic-table ref="basicTableRef" :table-columns="tableColumns" :query-params="queryParams" :selection="true" :api-fn="apiFn">
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
```

# Props Table

|  属性  |   类型  | 默认值 |  字段说明 |
| :----: | :----: | :----: |  :----:  |
| selection | Boolean | false | 是否显示表格首列的复选框 |
| tableHeight | Number | 500 | 表格的默认高度 |
| queryParams | Object | {} | 表单的查询参数 |
| tableColumns | Array | [] | 表头的配置项 |
| apiFn        | Funtion | () => {} | 表格的分页查询api |
| isBeforeFetch | [Function, undefined] | undefined | 表格查询前对入参过滤处理 |
| isAfterFetch | [Function, undefined] | undefined | 表格查询后对数据过滤处理 |
| isImmediateQuery | Boolean | true | 页面加载就执行查询表格数据 |
| queryBtnName | String | 查询 | 查询按钮名称 |
| resetBtnName | String | 重置 | 重置按钮名称 |

# Props Pagination

|  属性  |   类型  | 默认值 |  字段说明 |
| :----: | :----: | :----: |  :----:  |
| total | Number | - | 总条目数 |
| page | Number | 1 | 当前页数，支持 .sync 修饰符 |
| limit | Number | 10 | 每页显示条目个数，支持 .sync 修饰符 |
| pageSizes | Number | [10, 20, 30, 40, 50, 100] | 每页显示个数选择器的选项设置 |
| layout | String | 'total, sizes, prev, pager, next, jumper' | 组件布局，子组件名用逗号分隔 |
| background | Boolean | false | 是否为分页按钮添加背景色 |
| hidden | Boolean | false | 是否隐藏组件 |