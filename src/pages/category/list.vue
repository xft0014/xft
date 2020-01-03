<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <el-table :data="customers">
            <el-table-column type="selection" width="55">
        </el-table-column>

            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column  label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler
                    (slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <el-dialog
  title="录入顾客信息"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
      <el-form-item label="*栏目名称">
         <el-input type="" v-model    ="form.name"></el-input>
      </el-form-item>
            <el-form-item label="*序号">
         <el-input  v-model="form.num"></el-input>
      </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModelHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>
<script>
import  request  from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.customers = response.data;
        })
        },
        submitHandler(){
        //this.form 对象 ---字符串---> 后台
        //通过request与后台进行交互,并且携带参数
         let url="http://localhost:6677/category/saveOrUpdate"
         request({//小括号方法调用
             url,
             method:"POST",
             headers:{
                 "Content-Type":"application/x-www-form-urlencoded"
             },
             data:querystring.stringify(this.form)
         }).then((response)=>{
             //请求结束  模态框关闭  this.visiable   false
             this.closeModelHandler();
             //刷新
             this.loadData();
             this.$message({
                 type:"success",
                 message:response.message
             })
         })
        },
        toDeleteHandler(id){
       this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id//+id：测试ID是否拿到 @绑定方法  ：绑定数据
          });
        })
        },
        toUpdateHandler(){
            this.visible = true;
        },
        closeModelHandler(){
            this.visible = false;
        },
        toAddHandler(){
            this.visible = true;
        }
    },
    //要向网页中存放的数据
    data(){
        return{
            visible:false,
            customers:[],
            form:{
                type:"栏目管理"
            }
        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕之后要执行的操作
        //let url = "http://134.175.154.93:6677/customer/findAll"
        this.loadData();
        
    }
}
</script>

<style scoped>

</style>