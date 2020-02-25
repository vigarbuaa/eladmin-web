<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <el-input v-model="input" type="textarea" placeholder="把名单输入此处" clearable size="medium" @change="checkPerson" />
      <el-input v-model="output" placeholder="未交人员" clearable />
    </div>
  </div>
</template>

<script>

export default {
  name: 'Worktool',

  data() {
    return {
      output: '',
      input: ''
    }
  },
  methods: {
    checkPerson: function() {
      var userlist = ['于建', '何枫', '刘亚坤', '刘宇航', '刘昌健', '刘祖兵', '吴文斌', '姜悦飞', '孙国昊', '孟琳贺', '张潇逸', '徐刚', '方学军', '曲方伟', '李伟', '李军', '杨善明', '杨蛟', '樊莹莹', '沈柏屹', '沈莹莹', '牟莹', '王兆谨', '王玉', '王瑞升', '王菁', '程书', '管斌', '蒋天一', '邵鲁杰', '雷丹', '魏江涛']
      var myarray = this.input.split(/[,\s\n]/)
      console.log(this.input)
      console.log(myarray)
      var ret = ''
      for (var i = 0; i < userlist.length; i++) {
        var elem = userlist[i]
        if (myarray.indexOf(elem) === -1) {
          ret += elem + ' '
        }
      }
      this.output = '' + ret
      console.log('----' + this.output)
    }
  }
}
</script>
<style scoped>
  .head-container {
  width :800px
   }
</style>
