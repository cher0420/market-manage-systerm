<template>
  <el-table
    v-loading="loading"
    :data="tableData"
    stripe
    style="width: 100%">
    <el-table-column
      prop="BotName"
      label="机器人名称"
      width="180">
    </el-table-column>
    <el-table-column
      prop="ID"
      label="代码模版ID"
      width="180">
    </el-table-column>
    <el-table-column
      prop="DomainName"
      label="领域">
    </el-table-column>
    <el-table-column
      prop="BotType"
      label="类型">
    </el-table-column>
    <el-table-column
    prop="DeployModel"
    label="部署模式">
      <template slot-scope="scope">
        <div>{{scope.row.DeployModel === 'share'?'共享部署':'独立部署'}}</div>
      </template>
    </el-table-column>
    <el-table-column
      prop="IsAutoCreate"
      label="自动部署">
      <template slot-scope="scope">
        <div>{{scope.row.IsAutoCreate === 'False'?'否':'是'}}</div>
      </template>
    </el-table-column>
    <el-table-column
      prop="deal"
      label="操作">
      <template slot-scope="scope">
        <el-button @click="edit(scope.row.ID)" type="text" size="small">修改</el-button>
        <el-button @click="deleteItem(scope.row.ID)" type="text" size="small">删除</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>
<script>
import {getList, request} from '../../utils/server'
import {BOTLIST, DELETEBOT} from '../../constants/api'
import store from '../../store'
import {REPLACE} from '../../store/mutation-types'

export default {
  name: 'tableList',
  data () {
    return {
    }
  },
  computed: {
    tableData () {
      return store.state.app.tableData
    },
    loading () {
      return store.state.app.loading
    }
  },
  beforeCreate: function () {
    getList(BOTLIST, 'BotInfo')
  },
  methods: {
    edit (v) {
      this.$router.push({path: '/bot/edit', query: { ID: v }})
    },
    deleteItem (v) {
      this.$confirm('确定是否删除?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.del(DELETEBOT, null, {ID: v})
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    del (api, key, params) {
      const baseData = JSON.stringify(params)
      request(api, key,
        {
          method: 'POST',
          body: baseData
        }
      ).then((res) => {
        store.dispatch(REPLACE, {loading: true})
        this.$message({
          type: 'success',
          message: '删除成功!',
          duration: 1000,
          onClose: () => {
            getList(BOTLIST, 'BotInfo')
          }
        })
      }
      ).catch(
        err => this.$message({
          type: 'error',
          message: `${err.ErrorCodes[0].ErrorMessage}`
        })
      )
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
</style>
