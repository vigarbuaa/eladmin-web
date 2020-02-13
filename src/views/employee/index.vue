<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <el-input v-model="query.value" clearable placeholder="输入搜索内容" style="width: 200px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <el-select v-model="query.type" clearable placeholder="类型" class="filter-item" style="width: 130px">
          <el-option v-for="item in queryTypeOptions" :key="item.key" :label="item.display_name" :value="item.key" />
        </el-select>
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="姓名">
            <el-input v-model="form.name" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="性别">
            <el-input v-model="form.sex" style="width: 50px;" />
          </el-form-item>
          <el-form-item label="公司">
            <el-input v-model="form.company" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="手机号码">
            <el-input v-model="form.phone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="身份证号">
            <el-input v-model="form.idCard" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="住址">
            <el-input v-model="form.address" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="位址">
            <el-select v-model="form.location" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.工作地点"
                :key="item.id"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>

          <el-form-item label="岗位名称">
            <el-input v-model="form.zhiwei" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="备注">
            <el-input v-model="form.remarks" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="家乡">
            <el-input v-model="form.hometown" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="邮箱">
            <el-input v-model="form.email" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column v-if="columns.visible('name')" prop="name" label="性名" />
        <!-- <el-table-column v-if="columns.visible('email')" prop="email" label="邮箱" /> -->
        <el-table-column v-if="columns.visible('phone')" prop="phone" label="手机号码" />
        <!-- <el-table-column v-if="columns.visible('idCard')" prop="idCard" label="身份证号" /> -->
        <el-table-column v-if="columns.visible('location')" prop="location" label="位址">
          <template slot-scope="scope">
            {{ dict.label.工作地点[scope.row.location] }}
          </template>
        </el-table-column>
        <!--    <el-table-column v-if="columns.visible('createTime')" prop="createTime" label="创建日期">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.createTime) }}</span>
          </template>
        </el-table-column>-->
        <el-table-column v-if="columns.visible('sex')" prop="sex" label="性别" />

        <!-- <el-table-column v-if="columns.visible('company')" prop="company" label="公司" /> -->
        <el-table-column v-if="columns.visible('zhiwei')" prop="zhiwei" label="岗位名称" />
        <!-- <el-table-column v-if="columns.visible('remarks')" prop="remarks" label="备注" /> -->
        <el-table-column v-if="columns.visible('hometown')" prop="hometown" label="家乡" />
        <el-table-column v-if="columns.visible('address')" prop="address" label="住址" />
        <el-table-column v-permission="['admin','employeeInfo:edit','employeeInfo:del']" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudEmployeeInfo from '@/api/employeeInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({ title: '本部门人员信息', url: 'api/employeeInfo', sort: 'id,desc', crudMethod: { ...crudEmployeeInfo }})
const defaultForm = { id: null, email: null, phone: null, idCard: null, location: null, createTime: null, sex: null, name: null, company: null, zhiwei: null, remarks: null, hometown: null, address: null }
export default {
  name: 'EmployeeInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  dicts: ['工作地点'],
  data() {
    return {
      permission: {
        add: ['admin', 'employeeInfo:add'],
        edit: ['admin', 'employeeInfo:edit'],
        del: ['admin', 'employeeInfo:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'email', display_name: '邮箱' },
        { key: 'phone', display_name: '手机号码' },
        { key: 'idCard', display_name: '身份证号' },
        { key: 'location', display_name: '位址' },
        { key: 'sex', display_name: '性别' },
        { key: 'name', display_name: 'name' },
        { key: 'company', display_name: '公司' },
        { key: 'zhiwei', display_name: '岗位名称' },
        { key: 'remarks', display_name: '备注' },
        { key: 'hometown', display_name: '家乡' },
        { key: 'address', display_name: '住址' }
      ]
    }
  },
  methods: {
    // 获取数据前设置好接口地址
    [CRUD.HOOK.beforeRefresh]() {
      const query = this.query
      if (query.type && query.value) {
        this.crud.params[query.type] = query.value
      }
      return true
    }
  }
}
</script>

<style scoped>

</style>
