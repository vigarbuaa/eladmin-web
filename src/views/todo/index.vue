<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <!--表格渲染-->
      <el-table
        ref="table"
        v-loading="crud.loading"
        :data="crud.data"
        size="small"
        style="width: 100%;"
        @selection-change="crud.selectionChangeHandler"
      >
        <el-table-column type="selection" width="55" />
        <!-- <el-table-column v-if="columns.visible('id')" prop="id" label="id" /> -->
        <el-table-column v-if="columns.visible('content')" prop="content" label="内容" />
        <el-table-column v-permission="['admin','todo:edit','todo:del']" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation :data="scope.row" :permission="permission" />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudTodo from '@/api/todo'
import CRUD, {
  presenter,
  header,
  form,
  crud
} from '@crud/crud'
  // import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({
  title: '待办事宜',
  url: 'api/todo',
  sort: 'id,desc',
  crudMethod: { ...crudTodo
  }
})
const defaultForm = {
  id: null,
  content: null,
  completed: null,
  createBy: null,
  createDate: null,
  remarks: null,
  delFlag: null
}
export default {
  name: 'Todo',
  components: {
    pagination,
    crudOperation,
    // rrOperation,
    udOperation
  },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  data() {
    return {
      permission: {
        add: ['admin', 'todo:add'],
        edit: ['admin', 'todo:edit'],
        del: ['admin', 'todo:del']
      },
      rules: {}
    }
  },
  methods: {
    // 获取数据前设置好接口地址
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>
<style scoped>

</style>
