<template>
<!--    商品展示-->
  <div class="zs-box">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>商品展示</span>
      </div>
<!--      表格开始-->
      <el-table
        :data="tableData"
        border
        style="width: 100%">
        <el-table-column
          prop="id"
          label="ID">
        </el-table-column>
        <el-table-column
          prop="title"
          label="标题">
        </el-table-column>
        <el-table-column
          prop="subTitle"
          label="子标题">
        </el-table-column>
        <el-table-column
          prop="bname"
          label="所属分类">
        </el-table-column>c
        <el-table-column
          prop="cname"
          label="">
        </el-table-column>
      </el-table>
      <!--    分页-->
      <div class="block">
        <span class="demonstration">调整每页显示条数</span>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page.sync="currentPage2"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="100"
          layout="sizes, prev, pager, next"
          :total=total>
        </el-pagination>
      </div>


    </el-card>
  </div>
</template>

<script>
  import {getAction} from '../../api/manage'



  export default {
    name: 'recycleBin',
      data(){
        return{
            tableData: [],
            currentPage2: 1,
            total:'',
            pagee:10
            // opseng:[]
        }
      },
      methods:{

          // 分页
          handleSizeChange(val) {
              this.pagee=val
              getAction('/kunze/spu/spuList',{pageSize:val}).then((res)=>{
                  this.tableData=res.result.list

              })
          },
          handleCurrentChange(val) {
              getAction('/kunze/spu/spuList',{pageSize:this.pagee,pageNo:val}).then((res)=>{
                  this.tableData=res.result.list

              })
          },
        int(){
            getAction('/kunze/spu/spuList',{pageSize:10}).then((res)=>{
                 this.tableData=res.result.list

            })
            getAction('/kunze/spu/spuList',{pageSize:1}).then((res)=>{
                this.total=res.result.pages
            })


        }
      },
      mounted() {
        this.int()
      }
  }
</script>

<style scoped>

</style>
