<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <el-table :data="customers">
            <el-table-column type="selection" width="55">
        </el-table-column>

            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品"></el-table-column>
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
  title="录入产品信息"
  :visible.sync="visible"
  width="60%">

  <el-form :model="form" label-width="80px">
      <el-form-item label="产品名称">
         <el-input type="" v-model    ="form.name"></el-input>
      </el-form-item>
            <el-form-item label="产品价格">
         <el-input v-model="form.price"></el-input>
      </el-form-item>
      <el-form-item label="所属栏目">
         <el-select v-model="form.countryType" placeholder="请选择">
      <el-option  v-for="item in countryList" :key="item.value" :label="item.label"
           :value="item.value"></el-option>
            <!-- <el-option label="家具养护" value="9202"></el-option>
	      <el-option label="生活急救箱" value="9392"></el-option>
	      <el-option label="洗护服务" value="9358"></el-option>
	      <el-option label="澳大利亚袋鼠" value="9999"></el-option> -->
  </el-select>
      </el-form-item> 
          <el-form-item label="介绍">
         <el-input v-model="form.categorId"></el-input>
      </el-form-item>
          <el-form-item label="产品主图">
              只能上传jpg/png文件，且不超过500kb
         <el-input v-model="form.photo"></el-input>
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
            let url = "http://localhost:6677/product/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.customers = response.data;
        })
        },
        submitHandler(){
        //this.form 对象 ---字符串---> 后台
        //通过request与后台进行交互,并且携带参数
         let url="http://localhost:6677/product/saveOrUpdate"
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
              let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
        }
        }, 
    data(){
        return{
            water: {
                    siteName: '',
                },
                dataOptions: [],//下拉菜单
            visible:false,
            countryList: [],
            customers:[],
            form:{
                type:"产品信息"
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