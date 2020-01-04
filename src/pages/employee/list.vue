<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <el-table :data="employees">
            <el-table-column  prop="id" label="编号"></el-table-column>
            <el-table-column prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="手机号"></el-table-column>
            <el-table-column prop="idCard" label="身份证号" width="200"></el-table-column>
            <el-table-column prop="bankCard" label="银行卡号" width="200"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler
                    (slot.row.id)">删除</a>  
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <el-dialog
  title="title"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
      <el-form-item label="用户名">
         <el-input v-model="form.username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
         <el-input type="password" v-model="form.password"></el-input>
      </el-form-item>
            <el-form-item label="姓名">
         <el-input  v-model="form.realname"></el-input>
      </el-form-item>
      <el-form-item label="性别">
          <el-radio-group v-model="form.gender">
        <el-radio label="男">男</el-radio>
        <el-radio label="女">女</el-radio>
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
    <el-button size="small" @click="closeModelHandler">取 消</el-button>
    <el-button size="small" type="primary" @clic，，k="submitHandler">确 定</el-button>
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
            let url = "http://localhost:6677/waiter/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.employees = response.data;
        })
        },
        submitHandler(){
        //this.form 对象 ---字符串---> 后台
        //通过request与后台进行交互,并且携带参数
         let url="http://localhost:6677/waiter/saveOrUpdate"
         request({//小括号方法调用
             url,
             method:"POST",
             headers:{
                 //告诉后台我的请求体中放的是查询字符串
                 "Content-Type":"application/x-www-form-urlencoded"
             },
             //请求体数据
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
        toUpdateHandler(row){
            //模态框的表单中显示当前行的信息
            this.from =row;
            this.visible = true;
        },
        closeModelHandler(){
            this.visible = false;
        },
        toAddHandler(){
              this.form={
               type:"employee"
           }
            this.visible = true;
        }
    },
    //要向网页中存放的数据
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
        //this为当前vue实例对象
        //vue实例创建完毕之后要执行的操作
        //let url = "http://134.175.154.93:6677/customer/findAll"
        this.loadData();
        
    }
}
</script>

<style scoped>

</style>
