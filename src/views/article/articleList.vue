<template>
  <div>
    <el-table
      :data="articletData"
      style="width: 100%"
      max-height="550">
      <el-table-column
        fixed
        prop="created"
        label="发布时间"
        width="180">
      </el-table-column>
      <el-table-column
        prop="title"
        label="文章标题">
      </el-table-column>
      <el-table-column
        prop="commentNum"
        label="评论数"
        width="100">
      </el-table-column>
      <el-table-column
        prop="praiseNum"
        label="点赞数"
        width="100">
      </el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        width="200">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'articleList',
  data () {
    return {
      articletData: []
    }
  },
  methods: {
    handleEdit (index, row) {
      console.log(index, row);
      this.$router.push({path: '/addArticle?articleId=' + row._id});
    },
    handleDelete (index, row) {
      console.log(index, row)
      let that = this
      this.$axios.post('/api/delArticle', {
        id: row._id
      })
        .then(response => {
          console.log(response)
          if (response.data.status === 1) {
            that.$message({
              showClose: true,
              message: response.data.msg,
              type: 'success'
            })
            that.getList()
          }
        })
        .catch(err => {
          console.log(err)
        })
    },
    getList () {
      let that = this
      this.$axios.get('/api/user/article')
        .then(function (response) {
          if (response.data.status === 0) {
            that.$message({
              showClose: true,
              message: response.data.msg,
              type: 'error'
            })
            return false
          }
          for (let i = 0; i < response.data.length; i++) {
            let d = new Date(response.data[i].created)
            let moth = (d.getMonth() + 1) < 10 ? '0' + (d.getMonth() + 1) : (d.getMonth() + 1)
            let date = d.getDate() < 10 ? '0' + d.getDate() : d.getDate()
            let hours = d.getHours() < 10 ? '0' + d.getHours() : d.getHours()
            let minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : d.getMinutes()
            let seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : d.getSeconds()
            response.data[i].created = d.getFullYear() + '-' + moth + '-' + date + ' ' + hours + ':' + minutes + ':' + seconds
          }
          console.log(response)
          that.articletData = response.data
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  },
  mounted () {
    this.getList()
  }
}
</script>

<style scoped>

</style>
