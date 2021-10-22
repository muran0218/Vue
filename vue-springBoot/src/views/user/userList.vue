<template>
<div>
    <el-table
    :data="tableData"
    border
    style="width: 100%">
    <el-table-column
      prop="id"
      label="id"
      width="80">
    </el-table-column>
    <el-table-column
      prop="username"
      label="姓名"
      width="120">
    </el-table-column>
      <el-table-column
      prop="password"
      label="密码"
      width="120">
    </el-table-column>
    <el-table-column
      prop="nickname"
      label="昵称"
      width="120">
    </el-table-column>
        <el-table-column
      prop="telphone"
      label="电话"
      width="120">
    </el-table-column>
        <el-table-column
      prop="address"
      label="地址"
      width="450">
    </el-table-column>
    <el-table-column
      prop="isAdmin"
      label="身份"
      width="80">
      <template slot-scope="scope">
          <span v-if="scope.row.isAdmin == 0">商户</span>
          <span v-if="scope.row.isAdmin == 1">管理员</span>
        </template>
    </el-table-column>
        <el-table-column
      prop="isDelete"
      label="用户状态"
      width="80">
        <template slot-scope="scope">
          <span v-if="scope.row.isDelete == 0">正常</span>
          <span v-if="scope.row.isDelete == 1">删除</span>
        </template>
    </el-table-column>
    <el-table-column label="操作">
        <template slot-scope="scope">
            <el-button type="primary" round @click="toUpdateUser(scope.row.id)">修改</el-button>
            <el-button type="danger" round @click="deleteUser(scope.row.id)">删除</el-button>
       </template>
    </el-table-column>

  </el-table>
            <pagination
    :total="total"
    :page.sync="listQuery.page"
    :limit.sync="listQuery.limit"
    @pagination="getList" />
</div>
  

</template>

<script>
import Pagination from '@/components/Pagination'
  export default {
        components: { Pagination },
    data() {
      return {
        tableData: [],
        total: 0,
        listQuery: {
            page: 1,
            limit: 10
        }
      }
    },
    mounted() {
        this.getList();
    },
    methods: {
        getList(){
            var obj = this;
            this.axios({
                method:"get",
                url:"http://localhost:8081/user/findAll/"+this.listQuery.page+"/"+this.listQuery.limit
            }).then(function(response){
                 var result = response.data;
                 if(result.coode == 1000){
                    obj.tableData = result.data.list;
                    obj.total = result.data.total;
                 }else{
                     console.log("请求数据失败")
                 }
    
            })
        },
        toUpdateUser(id){
            this.$router.push("/user/updateUser/"+id)
        },
        deleteUser(id){
            const obj = this;
        this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            this.axios({
               method:"post",
               url:"http://localhost:8081/user/removeUserById/"+id
           }).then(function(respones){
                obj.$message({
                    type: 'success',
                    message: '删除成功!'
                });
                obj.getList();
           })
        }).catch(() => {
          obj.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
        }
    },


  }
</script>