<template>
    <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--表格开始-->
        <el-table :data="orders">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="orderTime" label="订单时间"></el-table-column>
            <el-table-column prop="total" label="数量"></el-table-column>
            <el-table-column prop="customerId" label="顾客编号"></el-table-column>
            <el-table-column prop="waiterId" label="员工编号"></el-table-column>
            <el-table-column prop="addressId" label="地址编号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot"><!--获取当前行所有信息-->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除 </a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)"> 修改</a>
                    <!--prevent阻止页面跳转（默认行为）-->
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->
        <!--对话框-->
        <el-dialog
        :title="title"
        :visible.sync="visible"
        width="60%">
        ---{{form}}
       <el-form :model="form" label-with="80px">
           <el-form-item label="时间">
               <el-input v-model="form.orderTime"/>
           </el-form-item>
            <el-form-item label="数量">
               <el-input v-model="form.total"/>
           </el-form-item>
            <el-form-item label="顾客编号">
               <el-input v-model="form.customerId"/>
            </el-form-item>
            <el-form-item label="员工编号">
               <el-input v-model="form.waiterId"/>
            </el-form-item>
            <el-form-item label="地址编号">
               <el-input v-model="form.addressId"/>
            </el-form-item>
       </el-form>
       <span slot="footer" class="dialog-footer">
       <el-button size="small" @click="closeModalHandler">取 消</el-button>
       <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
       </span>
       </el-dialog>
       <!--对话框结束-->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
      //要向网页中显示的数据
    data(){
        return{
            title:"录入栏目信息",
            visible:false,
            orders:[],
            form:{
                type:"order"
            }
        }
    },
    created(){
        this.loadData()
    },
    //存放网页中需要调用的方法
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/order/saveOrUpdate";
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
            let url="http://localhost:6677/order/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.orders=response.data;
            })            
        },
         toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/order/deleteById?id="+id;
        request.get(url).then((response)=>{
          //刷新数据
          this.loadData();
          //提示结果
          this.$message({
          type: 'success',
          message: response.message
        });
        })       
      })     
    },
      toUpdateHandler(row){
      //模态框表单中显示出当前行的信息
      this.form=row;
      this.visible = true;
    },
     toAddHandler(){
      this.form={
        type:"order"
      }
      this.visible = true;
     },
        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>

<style scoped>

</style>