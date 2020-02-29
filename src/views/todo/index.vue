<template>
  <div class="app-container">
    <!-- https://blog.csdn.net/csdnear/article/details/79467751 -->
    <div class="head-container">To do list Example</div>
    <el-input v-model="input" size="medium" placeholder="帅哥,你打算下一步干点what" clearable @change="addTodo" />
    <el-row v-for="(item, index) in todos" :key="index" class="list-row">
      <el-col :xs="2" :sm="1" :md="1" :lg="1" :xl="1" class="check" :class="{ red: !todos[index].completed, 'green': todos[index].completed }">
        <el-checkbox v-model="item.completed" size="mini" @change="complete(item)" />
      </el-col>
      <el-col :xs="20" :sm="22" :md="22" :lg="22" :xl="22">
        <input v-model="item.content" type="text" class="ipcont" :class="{done: todos[index].completed}">
      </el-col>
      <el-col :xs="2" :sm="1" :md="1" :lg="1" :xl="1" class="close">
        <i class="el-icon-close" @click="del(index,item.id)" />
      </el-col>
    </el-row>
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
    complete: function(item) {
      console.log(' complete func : ')
      console.log(item)
      const config = {
        headers: {
          'Authorization': getToken()
        }
      }
      var completed = item.completed ? 1 : 0
      axios.put('http://localhost:8000/api/todo/udpateStatus', { 'completed': completed, 'id': item.id }, config)
        .then(function(response) {
          console.log(response)
        }).catch(function(error) {
          console.log(error)
        })
    },
    del: function(index, id) {
      console.log(' del func : ' + id)
      const config = {
        headers: {
          'Authorization': getToken()
        }
      }
      axios.post('http://localhost:8000/api/todo/del', [id], config)
        .then(function(response) {
          console.log(response)
        }).catch(function(error) {
          console.log(error)
        })
      this.todos.splice(index, 1)
    },

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
#app {
      font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
      color: #756C83;
    }

    .el-header {
      text-align: center;
    }

    .todoinput {
      margin-bottom: 40px;
    }

    .list-row {
      height: 40px;
      background-color: #fbfbfb;
      margin-bottom: 5px;
    }

    .check {
      text-align: center;
      line-height: 40px;
    }

    .red {
      border-left: #ef5f65 2px solid;
    }

    .green {
      border-left: #B9E1DC 2px solid;
    }

    .ipcont {
      width: 90%;
      margin-top: 8px;
      border: 0;
      line-height: 24px;
      background-color: transparent;
      font-size: 16px;
      color: #756C83;
    }

    .close {
      text-align: center;
      line-height: 40px;
    }

    .el-icon-close {
      cursor: pointer;
    }

    .el-icon-close:hover {
      color: #ef5f65;
    }

    .done {
      text-decoration: line-through;
    }
</style>
