<template>
<div>
   <!--查询表单-->
          
        <el-form :inline="true" :rules="rules" :model="searchMap" class="demo-form-inline" ref="searchMap" >
          <el-form-item label="活动名称" prop="name">
            <el-input v-model="searchMap.name" placeholder="活动名称"></el-input>
          </el-form-item>
          <el-form-item label="活动日期" prop="starttime" >
            <el-date-picker  type="date" placeholder="选择开始日期" v-model="searchMap.starttime" ></el-date-picker>           
          </el-form-item>
           <el-form-item label="活动日期" prop="endtime" >           
            <el-date-picker  type="date" placeholder="选择截止日期" v-model="searchMap.endtime" ></el-date-picker>
          </el-form-item>
          <el-button type="primary" @click="fetchData2()">查询</el-button>
           <el-button @click="resetForm('searchMap')">重置</el-button>
        </el-form>
        
  <el-table 
  :data="list" border style="width: 100%"  > 
    <el-table-column  prop="id"  label="活动ID"   width="180"></el-table-column>
    <el-table-column  prop="name"  label="活动名称"  width="180"></el-table-column>
    <el-table-column  prop="sponsor"  label="主办方"  width="180"></el-table-column>
    <el-table-column  prop="address"  label="活动地址"   width="180"></el-table-column>
    <el-table-column  prop="starttime"  label="开始日期"  width="180"></el-table-column>
    <el-table-column  prop="endtime"   label="结束日期"   width="180"></el-table-column>
  </el-table>
  <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="this.currentPage"
      :page-sizes="[5, 10, 20]"
      :page-size="this.pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
  </el-pagination>
</div>
</template>

<script>
import gatheringApi from '@/api/gathering'

export default {
  //定义全局变量
    data(){
      return {
          list:[],
          total:0,//总记录数
          currentPage:1,//当前页
          pageSize:5,//每页的大小
          searchMap:{},//查询表单绑定变量
          rules: {
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ]
          }
      }
      
    },
     //打开页面时创建时调用的方法
    created(){
        this.fetchData2();
    },
    methods:{
        fetchData(){
           gatheringApi.getList().then(response => {
                this.list=response.data
           });
        },
        fetchData2(){
          gatheringApi.search(this.currentPage,this.pageSize,this.searchMap).then(response=>{
            this.list=response.data.records
            this.total=response.data.total
          })  
        },
        //监听 pagesize 改变的事件
    handleSizeChange(newsize){
     //这里conso 选中第几页 最新的值
     console.log(newsize)
    //最新的条数（newsize）赋值给 动态的 pageSize
     this.pageSize = newsize
    //获取到最新一页显示的数据  重新发送axios请求 这里是封装好的请求方法
     this.fetchData2()
 },


 // 监听 页码值 改变的事件
  handleCurrentChange(newPage) {
    console.log(newPage)
   //把最新的页码（newPage）赋值给 动态的 currentPage
    //this.currentPage= newPage
    //获取到最新显示的页码值  重新发送axios请求 这里是封装好的请求方法
   this.fetchData2()
 },
  
  //重置表单中的内容
    resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
}
</script>
