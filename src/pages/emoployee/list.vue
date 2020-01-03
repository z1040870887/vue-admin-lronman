<template>
    <div>
        <!--按钮-->
        <div>
            <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
            <el-button size="small" type="danger">批量删除</el-button>
        </div>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="employees">
           <el-table-column fixed="left"  prop= "id" label="编号"></el-table-column>
           <el-table-column fixed="left"  prop= "username" label="姓名"></el-table-column>
           <el-table-column prop= "gender" label="性别"></el-table-column>
           <el-table-column width="120"  prop= "telephone" label="手机号"></el-table-column>
           <el-table-column width="200"  prop= "idCard" label="身份证号"></el-table-column>
           <el-table-column width="200" prop= "bankCard" label="银行卡号"></el-table-column>
           <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
               <a href="" @click.prevent="toDeleteHandler(slot.row.id)" > 删除 </a>
               <a href="" @click.prevent="toUpdateHandler(slot.row)" > 修改 </a>
               </template> 
           </el-table-column>
           </el-table>
       <!--/表格-->
       <!--分页-->
        <el-pagination layout="prev,pager,next" :total="50"></el-pagination>
       <!--/分页-->
       <!--模态框-->
        <el-dialog 
         :title="title"
         :visible.sync="visible"
         width="60%">
         <el-form :model="form" label-width="80px">
             <el-form-item label="用户名">
                 <input v-model="form.username"/>
             </el-form-item>
             <el-form-item label="密码">
                 <input type="password" v-model="form.password"/>
             </el-form-item>
             <el-form-item label="姓名">
                 <input v-model="form.gender"/>
             </el-form-item>
             <el-form-item label="性别">
            <el-radio-group v-model="form.gender">
                        <el-radio label="男">男</el-radio>
                      <el-radio label="女">女</el-radio>
             </el-radio-group>
             </el-form-item>
             <el-form-item label="手机号">
                 <input v-model="form.telephone"/>
             </el-form-item>
             <el-form-item label="身份证号">
                 <input v-model="form.idCard"/>
             </el-form-item>
             <el-form-item label="银行卡号">
                 <input v-model="form.bankCard"/>
             </el-form-item>
         </el-form>
         <span slot="footer" class="dialog-footer">
             <el-button size="small"  @click="closeModalHandler">取消</el-button>
             <el-button size="small" type="primary" @click="submitHandlar">确定</el-button>
         </span>
        </el-dialog>
       <!--模态框-->
    </div>
</template>

<script>
import request from '@/utils/request/' //自定义库
import querystring from 'querystring'//系统库
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{  

            }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods:{
        //提交
        submitHandlar(){
            let url = "http://134.175.154.93:6677/waiter/saveOrUpdate"
            request({
                url,
                method:"post",
                //告诉后台我的请求体中放的是查询字符字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //刷新
                    this.loadData();
                //提示信息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })  
        },
        //重载员工数据
        loadData(){
            //this->vue实例。通过Vue实例访问该
            let url = "http://134.175.154.93:6677/waiter/findAll"
            request.get(url).then((response)=>{
                this.employees =  response.data;
            })
        },
        toAddHandler(){
            this.title = "录入员工信息"
            this.visible = true;
        },
        closeModalHandler(){
            this.visible = false;
        },
        toDeleteHandler(id){
             //确认
            this.$confirm( '此操作将永久删除文件，是否继续？','提示',{
                confirmButtonText :'确定',
                cancelButtonText:'取消',
                type:'warning'
            }).then(() =>{
                this.$message({
                    type:'success',
                    message:'删除成功！'+id
                });
            })
        },
        toUpdateHandler(row){
            this.title = "修改员工信息"
            this.visible= true;
        }
}
}
</script>
<style scoped>

</style>