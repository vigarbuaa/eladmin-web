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
          <el-form-item label="申请人">
            <el-input v-model="form.proposer" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标的物类型">
            <el-input v-model="form.subjectType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标的物">
            <el-input v-model="form.subject" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="数量">
            <el-input v-model="form.number" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="金额">
            <el-input v-model="form.money" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="供应商">
            <el-input v-model="form.supplier" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="合同日期">
            <el-date-picker v-model="form.contractDate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预算号">
            <el-input v-model="form.budget" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="remarks">
            <el-input v-model="form.remarks" :rows="3" type="textarea" style="width: 370px;" />
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
        <el-table-column v-if="columns.visible('proposer')" prop="proposer" label="申请人" width="60" />
        <el-table-column v-if="columns.visible('subjectType')" prop="subjectType" label="标的物类型" width="100" />
        <el-table-column v-if="columns.visible('subject')" prop="subject" label="标的物" width="200" />
        <!-- <el-table-column v-if="columns.visible('number')" prop="number" label="数量" /> -->
        <el-table-column v-if="columns.visible('money')" prop="money" label="金额" width="100" />
        <el-table-column v-if="columns.visible('supplier')" prop="supplier" label="供应商" width="200" />
        <el-table-column v-if="columns.visible('contractDate')" prop="contractDate" label="合同日期" width="100">
          <template slot-scope="scope">
            <span>{{ parseTime(scope.row.contractDate).split(" ")[0] }}</span>
          </template>
        </el-table-column>
        <el-table-column v-if="columns.visible('budget')" prop="budget" label="预算号" />
        <el-table-column v-permission="['admin','interconnectContract:edit','interconnectContract:del']" label="操作" width="150px" align="center">
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
import crudInterconnectContract from '@/api/interconnectContract'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

// crud交由presenter持有
const defaultCrud = CRUD({ title: '物联相关合同', url: 'api/interconnectContract', sort: 'id,desc', crudMethod: { ...crudInterconnectContract }})
const defaultForm = { id: null, proposer: null, subjectType: null, subject: null, number: null, money: null, supplier: null, contractDate: null, budget: null, remarks: null }
export default {
  name: 'InterconnectContract',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(defaultCrud), header(), form(defaultForm), crud()],
  data() {
    return {
      permission: {
        add: ['admin', 'interconnectContract:add'],
        edit: ['admin', 'interconnectContract:edit'],
        del: ['admin', 'interconnectContract:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'proposer', display_name: '申请人' },
        { key: 'subjectType', display_name: '标的物类型' },
        { key: 'subject', display_name: '标的物' },
        { key: 'money', display_name: '金额' },
        { key: 'supplier', display_name: '供应商' },
        { key: 'contractDate', display_name: '合同日期' },
        { key: 'budget', display_name: '预算号' }
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
