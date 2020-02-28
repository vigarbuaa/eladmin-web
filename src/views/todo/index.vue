<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <el-input v-model="input" size="medium" placeholder="帅哥,你打算下一步干点what" clearable @change="addTodo" />
      <el-table ref="table" :data="todos" size="small" style="width: 100%;">
        <!-- @selection-change="crud.selectionChangeHandler"> -->
        <el-table-column type="selection" width="55" />
        <!-- <el-table-column v-if="columns.visible('id')" prop="id" label="id" /> -->
        <el-table-column prop="content" width="500" label="内容" />
      </el-table>
    </div>
  </div>
</template>
<script>

import axios from 'axios'
import {
  getToken
} from '@/utils/auth' // getToken from cookie

export default {
  name: 'Todo',
  components: {},
  data() {
    return {
      rules: {},
      input: '',
      todos: []
    }
  },
  mounted: function() {
    // this.todos = crud.data
    var _this = this
    console.log('call get todo list')
    const config = {
      headers: {
        'Authorization': getToken()
      }
    }
    // this.todos
    axios.get('http://localhost:8000/api/todo', config)
      .then(function(response) {
        // console.log(response)
        // console.log(response.data.content)
        _this.todos = response.data.content
      }).catch(function(error) {
        console.log(error)
      })
  },
  methods: {
    addTodo: function() {
      let newid = 0
      console.log('add to do triggered!!!' + this.input)
      const config = {
        headers: {
          'Authorization': getToken()
        }
      }
      axios.post('http://localhost:8000/api/todo', {
        'content': this.input.trim()
      }, config)
        .then(function(response) {
          console.log(response)
          newid = response.data.id
        }).catch(function(error) {
          console.log(error)
        })

      this.todos.unshift({
        id: newid,
        content: this.input.trim(),
        completed: false
      })
      console.log('debug---')
      this.input = ''
    }
  }
}
</script>
<style scoped>
.head-container {
  width: 600px
}
</style>
