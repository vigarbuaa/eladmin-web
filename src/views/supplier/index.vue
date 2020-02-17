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
        <el-date-picker
          v-model="query.founddate"
          :default-time="['00:00:00','23:59:59']"
          type="daterange"
          range-separator=":"
          size="small"
          class="date-item"
          value-format="yyyy-MM-dd HH:mm:ss"
          start-placeholder="founddateStart"
          end-placeholder="founddateEnd"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">

          <el-form-item label="公司名称">
            <el-input v-model="form.companyname" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="名称缩写">
            <el-input v-model="form.shortname" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="成立日期">
            <el-date-picker v-model="form.founddate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="企业类型">
            <el-input v-model="form.orgtype" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="法人">
            <el-input v-model="form.chairman" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="注册资本">
            <el-input v-model="form.regcapital" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="负责人">
            <el-input v-model="form.manager" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="注册地">
            <el-input v-model="form.regaddr" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="办公地">
            <el-input v-model="form.officeaddr" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="邮编">
            <el-input v-model="form.officezipcode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="公司电话">
            <el-input v-model="form.comptel" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="公司简介">
            <el-input v-model="form.compintro" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="业务范围">
            <el-input v-model="form.bizscope" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="省份">
            <el-input v-model="form.provinceName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="城市名称">
            <el-input v-model="form.cityName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="入档日期">
            <el-input v-model="form.applyDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="申请人">
            <el-input v-model="form.proposer" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="buscode">
            <el-input v-model="form.buscode" style="width: 370px;" />
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
        <el-table-column v-if="columns.visible('companyname')" prop="companyname" label="公司名称" width="100" />
        <el-table-column v-if="columns.visible('founddate')" prop="founddate" label="成立日期" width="100">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.founddate) }}</span>
          </template>
        </el-table-column>
        <el-table-column v-if="columns.visible('chairman')" prop="chairman" label="法人" width="50" />
        <el-table-column v-if="columns.visible('regcapital')" prop="regcapital" label="注册资本" width="50" />
        <el-table-column v-if="columns.visible('bizscope')" prop="bizscope" label="业务范围" width="560" />
        <el-table-column v-if="columns.visible('provinceName')" prop="provinceName" label="省份" width="50" />
        <el-table-column v-permission="['admin','supplier:edit','supplier:del']" label="操作" width="100" align="center">
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
import crudSupplier from '@/api/supplier'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({ title: '供方单位详细信息', url: 'api/supplier', sort: 'id,desc', crudMethod: { ...crudSupplier }})
const defaultForm = { buscode: null, id: null, companyname: null, shortname: null, founddate: null, orgtype: null, chairman: null, regcapital: null, manager: null, regaddr: null, officeaddr: null, officezipcode: null, comptel: null, compintro: null, bizscope: null, provinceName: null, cityName: null, applyDate: null, proposer: null }
export default {
  name: 'Supplier',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  data() {
    return {
      permission: {
        add: ['admin', 'supplier:add'],
        edit: ['admin', 'supplier:edit'],
        del: ['admin', 'supplier:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'buscode', display_name: 'buscode' },
        { key: 'companyname', display_name: '公司名称' },
        { key: 'shortname', display_name: '名称缩写' },
        { key: 'orgtype', display_name: '企业类型' },
        { key: 'chairman', display_name: '法人' },
        { key: 'regcapital', display_name: '注册资本' },
        { key: 'bizscope', display_name: '业务范围' },
        { key: 'provinceName', display_name: '省份' },
        { key: 'cityName', display_name: '城市名称' },
        { key: 'proposer', display_name: '申请人' }
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
