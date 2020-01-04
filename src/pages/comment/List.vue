<template>
<!-- 评论管理 -->
  <div>
    <!-- 按钮 -->
    <div>
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small" >批量删除</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="comments">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="content" label="内容"></el-table-column>
      <el-table-column prop="commentTime" label="评论时间"></el-table-column>
      <el-table-column prop="orderId" label="序列编号"></el-table-column>
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
      {{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="内容">
          <el-input v-model="form.content"></el-input>
        </el-form-item>
        <el-form-item label="评论时间">
          <el-input v-model="form.commentTime"></el-input>
        </el-form-item>
        <el-form-item label="序列编号">
          <el-input v-model="form.orderId"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
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
            title:"录入评论信息",
            visible:false,
            comments:[],
            form:{
                type:"comment"
            }
        }
    },
    created(){
        this.loadData()
    },
    //存放网页中需要调用的方法
    methods:{
      submitHandler(){
        let url = "http://localhost:6677/comment/saveOrUpdate";
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
            let url="http://localhost:6677/comment/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.comments=response.data;
            })
        },
        toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/comment/deleteById?id="+id;
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
            this.form=row;
            this.title="修改评论信息"
            this.visible=true;
        },
        toAddHandler(){
            this.form={
                  type:"comment"
              }
            this.title="录入评论信息"
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