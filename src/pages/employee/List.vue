<template>
<!-- 员工管理 -->
  <div>
    <!-- 按钮 -->
    <div>
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small" @click="toDeleteHandler">批量删除</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="employees">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="realname" label="真实姓名"></el-table-column>
      <el-table-column prop="age" label="年龄"></el-table-column>
      <el-table-column prop="gender" label="性别"></el-table-column>
      <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
      <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
      <el-table-column width="120" prop="telephone" label="联系方式"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <!-- slot用于获取当前行数据 -->
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <!-- <el-pagination layout="prev, pager, next" :total="50"></el-pagination> -->
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
      <!-- 测试：{{form }} -->
      <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="性别">
         <el-radio-group v-model="radio">
          <el-radio :label="男">男</el-radio>
          <el-radio :label="女">女</el-radio>
        </el-radio-group>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input v-model="form.idCard"></el-input>
        </el-form-item>
        <el-form-item label="银行卡号">
          <el-input v-model="form.bankCard"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="closeModalHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
      //要向网页中显示的数据
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        this.loadData()
    },
    //存放网页中需要调用的方法
    methods:{
      submitHandler(){
        let url = "http://localhost:6677/waiter/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
      },
      loadData(){
            // this-->vue实例,通过vue实例访问该实例中的数据
            let url="http://localhost:6677/waiter/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.employees=response.data;
            })
        },
        toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/waiter/deleteById?id="+id;
            request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示结果
                this.$message({
                type: 'success',
                message:response.message
            });
            });
        })
        },
        toUpdateHandler(row){
            this.title="修改员工信息"
            this.visible=true;
        },
        toAddHandler(){
            this.form={
                type:"employee"
            }
            this.title="录入员工信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>
<style scoped>
 
</style>