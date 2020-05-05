<template>
<!--    商品规格管理-->
  <div>

    <el-tree :data="nrr" :props="defaultProps" @node-click="handleNodeClick" ></el-tree>
<!--    弹窗-->
    <el-dialog title="商品规格" :visible.sync="dialogTableVisible">
<!--      <el-button type="primary" class="addbtn">添加参数规格</el-button>-->
      <template v-for="x in csggzs" >
      <el-card class="box-card" style="margin-bottom: 10px">
        <div slot="header" class="clearfix">
          <span>{{x.group}}</span>
          <el-button  icon="el-icon-delete" style="float: right">删除</el-button>
        </div>
        <el-table
          :data="x.params"
          style="width: 100%"
          max-height="250">
          <el-table-column
            fixed
            prop="k"
            label="品牌">
          </el-table-column>
          <el-table-column
            fixed
            prop="options"
            label="参数">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button @click="modifyParameters(scope.row)" type="text" size="small">修改</el-button>
              <el-button @click="deleteParameters(scope.row)" type="text" size="small">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-button class="addxsx">添加新的属性</el-button>
      </el-card>
      </template>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogTableVisible = false" class="qrmb">保存模板规格</el-button>
        <el-button @click="xiugai">添加新的分组</el-button>
      </div>
    </el-dialog>



    <!--    添加新的分组天出框-->
    <el-dialog title="添加新的分组" :visible.sync="dialogFormVisible">
      <el-form :model="formAddfz">
        <el-form-item label="分组名称" :label-width="formLabelWidth">
          <el-input v-model="formAddfz.group" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="qxAddfz">取 消</el-button>
        <el-button type="primary" @click="qrAddfz">确 定</el-button>
      </div>
    </el-dialog>

  </div>




</template>

<script>
  import {getAction} from '../../../api/manage'
  import {postAction} from '../../../api/manage'
  import {xiufz} from '../../../api/interface'
  import { axios } from '@/utils/request'
  export default {
    name: 'describe',
      data(){
        return{
            nrr: [],
            data: [],
            defaultProps: {
                children: 'childrenList',
                label: 'name'
            },
            ids:[],
            nnn:{},
            dialogTableVisible:false,
            csggzs:[],
            id:'' , //点击的三级分类的id
            dialogFormVisible:false,
            formLabelWidth: '120px',
            formAddfz:{
              group:'',
              params:[]
            }



        }
      },
      methods:{
          // 点击新加分组取消按钮
        qxAddfz(){
          this.dialogFormVisible = false
          this.formAddfz.group=''
        },
          // 点击新加分组确认按钮
        qrAddfz(){
          if(this.formAddfz.group==''){
            this.$message.error('分组名称不能为空');
          }else {
            this.dialogFormVisible = false
            this.csggzs.push(this.formAddfz)
            let nyj=this.csggzs
            let nn={
              categoryId:this.id,specifications:JSON.stringify(this.csggzs)
            }

            postAction('/kunze/spec/updateSpec',nn).then((res)=>{
              console.log(res);
              // if(res.result!=null){
              //   this.formAddfz.group=''
              // }
              this.formAddfz.group=''
            })


          }
        },
          // 添加新的分组
        xiugai(){
          this.dialogFormVisible = true
        },
          // 修改参数
        modifyParameters(){

        },
        //删除参数
        deleteParameters(){

        },
          handleNodeClick(data) {
            if(data.isParent==0){
                  this.id=data.id
                  getAction('/kunze/spec/specList',{categoryId:data.id}).then((res)=>{
                     if(res.result==null){
                         this.$message({
                             message: '该商品还没有规格参数',
                             type: 'warning'
                         });
                     }else {
                          this.csggzs=JSON.parse(res.result.specifications)


                       this.csggzs.forEach(e=>{
                           e.params.forEach((e)=>{
                             e.options=JSON.stringify(e.options).replace("[","")
                             e.options=e.options.replace("]","")
                           })
                       })
                          this.dialogTableVisible=true
                  }
                  })
              }
          },
        int(){
            getAction('/kunze/category/qryList',{pid:""}).then((res)=>{
                res.result.forEach(e=>{
                    if(e.parentId==0){
                        this.nrr.push(e)
                    }
                })

            })

        },

      },
      mounted() {
        this.int()
      }

  }
</script>

<style scoped>
  .addbtn{
    margin-bottom: 10px;
  }
  .addxsx{
    float: right;
    margin: 10px 0;
  }
  /deep/.cell{
    text-align: center;
  }

</style>
