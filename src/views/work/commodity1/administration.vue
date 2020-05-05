<template>
<!--    管理分类-->
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <span>分类管理</span>
    </div>
    <div>
      <el-button type="primary" icon="el-icon-arrow-left" circle @click="blackfenlei"></el-button>
      <el-button type="primary" round class="flgl-btn" @click="dialogFormVisible = true">新增分类</el-button>
      <el-button type="primary" round class="flgl-btn" @click="plscfl()">批量删除</el-button>

      <el-table
        :data="tableData"
        border
        style="width: 100%"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column
          prop="name"
          label="分类名称">
        </el-table-column>
<!--        <el-table-column-->
<!--          label="新增子类">-->
<!--          <template slot-scope="scope">-->
<!--            <el-button type="primary" round>新增子类</el-button>-->
<!--          </template>-->
<!--        </el-table-column>-->
        <el-table-column
          label="新增子类">
          <template slot-scope="scope">
            <el-button type="primary" round @click="ckzfl(scope.row)">查看子分类</el-button>
          </template>
        </el-table-column>
        <el-table-column
          label="排序">
          <template slot-scope="scope">
            <template v-if="scope.row.index!=1">
              <el-button type="primary" icon="el-icon-top" circle   @click="sheng(scope.row)"></el-button>
            </template>
            <template v-if="scope.row.index!=2">
              <el-button type="primary" icon="el-icon-bottom" circle @click="jiang(scope.row)"></el-button>
            </template>

          </template>
        </el-table-column>
        <el-table-column
          fixed="right"
          label="操作">
          <template slot-scope="scope">
            <el-button  type="text" size="small" @click="xgan(scope.row)">修改</el-button>
            <el-button type="text" size="small" @click="gldele(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>



    </div>



<!--    添加分类弹窗-->

    <el-dialog title="添加分类" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="分类名称" :label-width="formLabelWidth">
              <el-input v-model="form.name" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="排序" :label-width="formLabelWidth">
              <el-input v-model="form.sort" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="父级分类" :label-width="formLabelWidth">
              <el-select v-model="form.parentId" placeholder="请选择父级分类" >
                <el-option label="顶级分类" value="0"></el-option>
                <template v-for="v in tableData">
                  <el-option :label="v.name" :value="v.id"></el-option>
                </template>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="是否为父级分类" :label-width="formLabelWidth">
              <el-select v-model="form.isParent" placeholder="请选择是否为父级分类" >
                <el-option label="是" value="1"></el-option>
                <el-option label="否" value="0"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="qxtj()">取 消</el-button>
        <el-button type="primary" @click="tianjia()">确 定</el-button>
      </div>
    </el-dialog>

<!--    修改分类弹出窗-->

    <el-dialog title="修改分类" :visible.sync="dialogFormVisible1">
      <el-form :model="form">
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="分类名称" :label-width="formLabelWidth">
              <el-input v-model="form.name" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="排序" :label-width="formLabelWidth">
              <el-input v-model="form.sort" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="父级分类" :label-width="formLabelWidth">
              <el-select v-model="form.parentId" placeholder="请选择父级分类" >
                <el-option label="顶级分类" value="0"></el-option>
                <template v-for="v in tableData">
                  <el-option :label="v.name" :value="v.id"></el-option>
                </template>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="是否为父级分类" :label-width="formLabelWidth">
              <el-select v-model="form.isParent" placeholder="请选择是否为父级分类" >
                <el-option label="是" value="1"></el-option>
                <el-option label="否" value="0"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="qxxg()">取 消</el-button>
        <el-button type="primary" @click="xiugai()">确 定</el-button>
      </div>
    </el-dialog>

  </el-card>
</template>

<script>
    import { deleteAction } from "../../../api/manage"
    import { getAction } from "../../../api/manage"
    import { addlei } from "../../../api/interface"
    import { xiulei } from "../../../api/interface"
    export default {
        data() {
            return {
                tableData: [],
                tableDatazi: [],
                dialogTableVisible: false,
                dialogFormVisible: false,
                dialogFormVisible1: false,
                form: {
                    name: '',
                    sort:'',
                    parentId:'',
                    isParent:'',
                    id:''
                },
                formLabelWidth: '120px',
                isdian:true,
                ids:[],
                isziys:[],

            };
        },
        methods: {
            // 批量删除
            plscfl(){
                if(this.ids.length==0){
                    this.$message.error('请选择要删除的分类');
                }else {
                    let nn=this.ids.toString()

                    this.$confirm('是否删除该分类?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        deleteAction('/kunze/category/categoryDels',{ids:nn}).then((res)=>{

                            if(res.code==200){
                                this.$message({
                                    type: 'success',
                                    message: '删除成功!'})
                                this.hqsyfl()
                                this.ids=[]
                            }else {
                                this.$message.error('删除失败');
                            }
                        }).cache(()=>{
                            this.$message.error('删除失败');

                        })
                    })
                }


            },
            // 全选
            handleSelectionChange(val) {
                this.ids=[]

                val.forEach(e=>{
                    this.ids.push(e.id)
                    // this.isziys=e.id
                })
            },
            // 点击返回按钮
            blackfenlei(){
                this.hqsyfl()
            },
            // 点击查看子分类
            ckzfl(row){ let arr3=[]
                this.tableData.forEach(e=>{

                    if(e.id==row.id){
                        arr3=e.childrenList
                    }
                })

                if(arr3.length==0){
                    this.$message({
                        type: 'success',
                        message: '此分类下没有子分类!'

                    })
                }else {
                    let arr=[]
                    arr3.forEach(e=>{
                        arr.push(e.sort)
                    })
                    arr.sort();
                    arr.sort(function (m, n) {
                        if (m < n) return -1
                        else if (m > n) return 1
                        else return 0
                    });
                    let arr1=[]
                    arr.forEach(e=>{
                        arr3.forEach(y=>{

                            if(e==y.sort){
                                arr1.push(y)
                                arr3.forEach((x,d)=>{
                                    if(y.id==x.id){
                                        arr3.splice(d,1)
                                    }
                                })
                            }
                        })
                    })

                    this.tableData=arr1
                    // console.log(this.tableData,666);


                    this.tableData[0].index=1
                    this.tableData[this.tableData.length-1].index=2
                }



            },
            // 点击修改那妞
            xgan(row){
                this.dialogFormVisible1 = true
                this.form.name=row.name
                this.form.sort=row.sort
                this.form.parentId=row.parentId
                this.form.isParent=row.isParent
                this.form.id=row.id
            },
            // 点击取消按钮
            qxxg(){
                this.dialogFormVisible1 = false
                this.form.name=''
                this.form.sort=''
                this.form.parentId=''
                this.form.isParent=''
            },
            // 点击修改确认按钮
            xiugai(){
                this.dialogFormVisible1 = false
                let nn=JSON.parse(JSON.stringify(this.form))
                xiulei(nn).then((res)=>{

                    this.hqsyfl()
                    this.form.name=''
                    this.form.sort=''
                    this.form.parentId=''
                    this.form.isParent=''
                })
            },
            // 点击取消添加按钮
            qxtj(){
                this.dialogFormVisible = false
                this.form.name=''
                this.form.sort=''
                this.form.parentId=''
                this.form.isParent=''
            },
            // 点击确定添加按钮添加分类
            tianjia(){
                this.dialogFormVisible = false

                let nn=JSON.parse(JSON.stringify(this.form))
                addlei(nn).then((res)=>{
                    this.hqsyfl()
                    this.form.name=''
                    this.form.sort=''
                    this.form.parentId=''
                    this.form.isParent=''
                })
            },
            // 删除分类
            gldele(m){

               let that=this

                getAction('/kunze/category/qryList',{pid:m}).then((res)=>{
                    that.isziys=[]
                    that.isziys=res.result

                    if(that.isziys.length==0){
                        this.$confirm('是否删除该分类?', '提示', {
                            confirmButtonText: '确定',
                            cancelButtonText: '取消',
                            type: 'warning'
                        }).then(() => {

                            deleteAction('/kunze/category/categoryDel',{id:m}).then((res)=>{
                                this.hqsyfl()
                                this.$message({
                                    type: 'success',
                                    message: '删除成功!'
                                });
                            })

                        }).catch(() => {
                            this.$message({
                                type: 'info',
                                message: '已取消删除'
                            });
                        });
                    }else
                    {
                        this.$confirm('此分类包含多个子分类, 是否删除?', '提示', {
                            confirmButtonText: '确定',
                            cancelButtonText: '取消',
                            type: 'warning'
                        }).then(() => {

                            deleteAction('/kunze/category/categoryDel',{id:m}).then((res)=>{
                                this.hqsyfl()
                                this.$message({
                                    type: 'success',
                                    message: '删除成功!'
                                });
                            })

                        }).catch(() => {
                            this.$message({
                                type: 'info',
                                message: '已取消删除'
                            });
                        });
                    }
                })




            },
            // 降序排列
            jiang(row) {
                let that=this
                let i=this.tableData.length
                let n,m
                let y=-1
                while (i--) {
                    if (this.tableData[i].id === row.id) {
                        n=i
                    }
                }


                m=this.tableData[n]
                this.$set(this.tableData,n,this.tableData[n-y])
                this.$set(this.tableData,n-y,m)


                this.tableData.forEach(e=>{
                    e.index=0
                })
                this.tableData.forEach((e,j)=>{
                    if(j==0){
                        e.index=1
                    }
                    if(j==that.tableData.length-1){
                        e.index=2
                    }
                })
            },
            // 升序排列
            sheng(row) {
                let that=this
                let i=this.tableData.length
                let n,m
                let y=1
                while (i--) {
                    if (this.tableData[i].id === row.id) {
                        n=i

                    }
                }



                m=this.tableData[n]
                this.$set(this.tableData,n,this.tableData[n-y])
                this.$set(this.tableData,n-y,m)



                this.tableData.forEach(e=>{
                    e.index=0

                })


                this.tableData.forEach((e,j)=>{

                    if(j==0){
                        e.index=1
                    }
                    if(j==that.tableData.length-1){
                        e.index=2
                    }
                })


            },


            hqsyfl(){
                getAction('/kunze/category/qryList',{pid:''}).then((res)=>{
                    let arr3=[]
                    res.result.forEach(e=>{
                        if(e.parentId==0){
                            arr3.push(e)
                        }
                    })


                    // let arr3=[]
                    // data.forEach(e=>{
                    //     if(e.isParent==1){
                    //         arr3.push(e)
                    //     }
                    // })
                    let arr=[]
                    arr3.forEach(e=>{
                        arr.push(e.sort)
                    })
                    arr.sort();
                    arr.sort(function (m, n) {
                        if (m < n) return -1
                        else if (m > n) return 1
                        else return 0
                    });
                    let arr1=[]
                    arr.forEach(e=>{
                        arr3.forEach(y=>{

                            if(e==y.sort){
                                arr1.push(y)
                                arr3.forEach((x,d)=>{
                                    if(y.id==x.id){
                                        arr3.splice(d,1)
                                    }
                                })
                            }
                        })
                    })

                    this.tableData=arr1




                    this.tableData[0].index=1
                    this.tableData[this.tableData.length-1].index=2

                }).catch((err)=>{

                })
            }

        },
        mounted(){

            this.hqsyfl()

        }
    };
</script>

<style scoped>
  .text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 18px;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }
  .clearfix:after {
    clear: both
  }

  .box-card {
    width:100%;

  }
  .flgl-btn{
    margin-bottom: 20px;
  }
  /deep/.el-table td, .el-table th.is-leaf{
    text-align: center;
  }
  /deep/.el-table--border th, .el-table__fixed-right-patch{
    text-align: center;
  }
  /deep/.el-select{
    width: 100%;
  }
</style>
