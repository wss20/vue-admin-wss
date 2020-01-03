<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
    
        <el-table :data="customers">
            <el-table-column  prop="id" label="编号"></el-table-column>
            <el-table-column  prop="realname" label="姓名"></el-table-column>
            <el-table-column  prop="telephone" label="联系方式"></el-table-column>
            <el-table-column  label="操作">
                <template v-slot="slot">
                    <!-- 获取当前行的数据 -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
         <!-- <el-pagination layout="prev, pager, next" :total="50"> 
  </el-pagination> -->

  <el-dialog
  title="录入顾客信息" :visible.sync="visible"
  width="60%" >
  ---{{form}}
  <!-- 可以看到输入信息，用于测试 -->
  <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        <!-- 双向数据绑定 -->
      </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>

      </el-form-item>
      <el-form-item label="真实姓名">
          <el-input  v-model="form.realname"></el-input>

      </el-form-item>
      <el-form-item label="手机号">
          <el-input  v-model="form.telephone"></el-input>

      </el-form-item>
  </el-form>
  <span></span>
  <span slot="footer" class="dialog-footer">
    <el-button  size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request' //表示src
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            // this->vue实例
        let url = "http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
     //将查询结果设置到customer中,this指向外部函数的this，如果是匿名函数，函数主属性的this->windows
            this.customers = response.data;

      })
        },
         submitHandler(){
            //this.form对象 ---字符串--->后台
            //json字符串，要查看具体要求
            //查询字符串
            //通过request与后台进行交互，并且携带参数
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,//可等价转换
                method:"POST",//错误在method后面加上s就会显示http协议无法返回网站，就会报错说network error
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
                // 转换成查询字符串

            }).then((response)=>{
                //请求结束，模态框关闭
                this.closeModalHandler();
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口
            let url="http://localhost:6677/customer/deleteById?id="+id
            // get传参的方式，和post不同
            request.get(url).then((response)=>{
                // 1.刷新数据
                this.loadData();
                // 2.提示结果
                this.$message({
                type: 'success',
                message: '删除成功!'
          });
        })
        })
        },
        toUpdateHandler(row){
            //模态框的表单中显示当前信息
            this.form=row;
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        
        },
        toAddHandler(){
            // 将form变成初始值
            this.form={
                type:"customer"
            }
            this.visible=true;
        }
       
    },//用于将模态框打开
    //用于存放要向网页显示的数据
  data(){
      return{
          visible:false,
          customers:[],
          form:{
              type:"customer"
          }
      }
  } ,
  created(){
      //vue实例创建完毕要进行的操作
     this.loadData()
  } 
}
</script>

<style scoped>

</style>