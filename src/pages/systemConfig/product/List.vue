<template>
    <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--表格开始-->
        <el-table :data="products">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属栏目"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot"><!--获取当前行所有信息-->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除 </a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)"> 修改 </a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)"> 详情</a>
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
            <el-form-item label="名称">
               <el-input v-model="form.name"/>
           </el-form-item>
            <el-form-item label="价格">
               <el-input v-model="form.price"/>
            </el-form-item>
            <el-form-item label="所属栏目">
                    <el-select v-model="form.categoryId" placeholder="请选择">
                        <el-option v-for="item in categoryIdArr"
                            :key="item.value" :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
            </el-form-item>
             <el-form-item label="介绍">
               <el-input
                type="textarea"
                :rows="2"
                placeholder="请输入内容"
                v-model="form.description">
                </el-input>
            </el-form-item>
             <el-form-item label="产品主图">
               <el-upload
                class="upload-demo"
                action="https://jsonplaceholder.typicode.com/posts/"
               :on-preview="handlePreview"
               :on-remove="handleRemove"
               :before-remove="beforeRemove"
                multiple
               :limit="3"
               :on-exceed="handleExceed"
               :file-list="fileList">
               <el-button size="small" type="primary">点击上传</el-button>
               <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
               </el-upload>
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
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            },
            categoryIdArr:[]
        }
    },
    created(){
        this.loadData()
        this.findCategoryId()
    },
    //存放网页中需要调用的方法
    methods:{
       // 查询顾客id
        findCategoryId(){
            let url="http://localhost:6677/category/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.categoryIdArr = response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/product/saveOrUpdate";
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
            let url="http://localhost:6677/product/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.products=response.data;
            })
            
        },
         toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/product/deleteById?id="+id;
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
            let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
        },

    closeModalHandler(){
      this.visible = false;
     }
    }
}
</script>

<style scoped>

</style>