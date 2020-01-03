<template>
    <div>
        <el-button type="primary" size="small"  @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>

        <el-table :data="categorys">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">详情</a>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination layout="prev, pager, next" :total="50"> 
        </el-pagination>
        <!-- <el-checkbox-group v-model="idList">
            <el-checkbox label="复选框 A"></el-checkbox>
            <el-checkbox label="复选框 B"></el-checkbox>
            <el-checkbox label="复选框 C"></el-checkbox>
  </el-checkbox-group> -->
<el-dialog :title="title" :visible.sync="visible" width="60%" >
        {{form}}
        <el-form  :model="form" width="60px">
            <el-form-item label="栏目名称">
                <el-input v-model="form.name"/>
            </el-form-item>
            <el-form-item label="序号">
                <el-input v-model="form.num"/>
            </el-form-item>
        </el-form>
    <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确定</el-button>
    </span>
</el-dialog>      
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入栏目信息",
            visible:false,
            categorys:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        // 在页面加载出来的时候加载数据
        this.loadData();
    },
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate";
            // 前端向后台发送请求，完成数据的保存的操作
            request({
                url,
                method:"post",
                // 告诉后台请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束，模态框关闭
                this.closeModalHandler();
                this.loadData();
                //提示消息
                this.$message({type:"success",message:response.message})
            })
        },
        // 重载产品数据
        loadData(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.categorys=response.data;
            })
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="添加栏目信息"
            this.visible=true;
        },
        toDeleteHandler(){
            //确认
            this.$confirm('此操作将永久删除该产品, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
        toUpdateHandler(row){
            this.title="修改栏目信息",
            this.visible=true;
        }
    },
}
</script>

<style scoped>

</style>