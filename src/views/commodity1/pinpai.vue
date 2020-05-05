<template>
<!--    商品品牌-->
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <span>分类管理</span>
    </div>
    <div>
      <el-button type="primary" round class="flgl-btn" @click="dialogFormVisible=true">添加品牌</el-button>
      <el-button type="primary" round class="flgl-btn" @click="plscpp()">批量删除</el-button>

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
          label="图片">
          <template slot-scope="scope">
            <img :src=scope.row.image alt="">
          </template>
        </el-table-column>

        <el-table-column
          prop="letter"
          label="商标">
        </el-table-column>
        <el-table-column
          prop="updateName"
          label="管理员">
        </el-table-column>
        <el-table-column
          prop="kid"
          label="kid">
        </el-table-column>
        <el-table-column
          prop="bid"
          label="bid">
        </el-table-column>
        <el-table-column
          prop="kname"
          label="商品名称">
        </el-table-column>
        <el-table-column
          prop="bname"
          label="品牌名称">
        </el-table-column>
        <el-table-column
          fixed="right"
          label="操作">
          <template slot-scope="scope">
            <el-button  type="text" size="small" @click="xiupp(scope.row)">修改</el-button>
            <el-button type="text" size="small" @click="ppdel(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>



    </div>
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


<!--    添加品牌-->
    <el-dialog title="添加品牌" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="品牌名称" :label-width="formLabelWidth">
              <el-input v-model="form.name" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="商标" :label-width="formLabelWidth">
              <el-input v-model="form.letter" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="KID" :label-width="formLabelWidth">
              <el-input v-model="form.kid" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="图片" :label-width="formLabelWidth">
              <el-upload
                class="avatar-uploader"
                action="https://jsonplaceholder.typicode.com/posts/"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload">
                <img v-if="imageUrl" :src="imageUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
          </el-col>
        </el-row>



      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="qxtj()">取 消</el-button>
        <el-button type="primary" @click="addpp()">确 定</el-button>
      </div>
    </el-dialog>

<!--    修改品牌-->
    <!--    添加分类-->
    <el-dialog title="修改品牌" :visible.sync="dialogFormVisible1">
      <el-form :model="xgform">
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="品牌名称" :label-width="formLabelWidth">
              <el-input v-model="xgform.name" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="10">
            <el-form-item label="商标" :label-width="formLabelWidth">
              <el-input v-model="xgform.letter" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="KID" :label-width="formLabelWidth">
              <el-input v-model="xgform.kid" autocomplete="off" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="10">
            <el-form-item label="图片" :label-width="formLabelWidth">
              <el-upload
                class="avatar-uploader"
                action="https://jsonplaceholder.typicode.com/posts/"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload">
                <img v-if="imageUrl" :src="imageUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
          </el-col>
        </el-row>



      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="qxxg()">取 消</el-button>
        <el-button type="primary" @click="qdxg()">确 定</el-button>
      </div>
    </el-dialog>




  </el-card>

</template>

<script>
  import {getAction} from '../../api/manage'
  import {deleteAction} from '../../api/manage'
  import {postAction} from '../../api/manage'
  export default {
    name: 'pinpai',
      data(){
        return{
            tableData: [],
            dialogFormVisible:false,
            dialogFormVisible1:false,
            form:{
                name:'' ,
                letter:'',
                image:'',
                kid:''
            },
            xgform:{
                name:'' ,
                letter:'',
                image:'',
                kid:''
            },
            formLabelWidth:'120px',
            imageUrl: '',
            currentPage2: 1,
            total:'',
            pagee:10,
            bids:[]
        }
      },
      methods:{
        // 点击修改确认按钮
          qdxg(){
              this.xgform.image=this.imageUrl
              this.xgform.kid=this.imageUrl
              let nn=JSON.parse(JSON.stringify(this.xgform))
              postAction('/kunze/brand/updateBrand',{brandVo:nn}).then((res)=>{

              })
          },
        // 点击修改取消按钮
          qxxg(){
              this.xgform.image=''
              this.xgform.name=''
              this.xgform.kid=''
              this.xgform.letter=''
              this.dialogFormVisible1=false
              this.imageUrl=''
          },
        // 点击修改按钮
          xiupp(row){
              this.dialogFormVisible1=true
              this.imageUrl=row.image
              this.xgform.name=row.bname
              this.xgform.letter=row.letter
              this.xgform.kid=row.bid



          },
        // 批量删除
          plscpp(){
              if(this.bids.length==0){
                  this.$message.error('请选择要删除的品牌');

              }else {
                  let nn=this.bids.toString()

                  this.$confirm('是否删除该品牌?', '提示', {
                      confirmButtonText: '确定',
                      cancelButtonText: '取消',
                      type: 'warning'
                  }).then(() => {
                      deleteAction('/kunze/brand/delBrands',{bids:nn}).then((res)=>{

                          if(res.code==200){
                              this.$message({
                                  type: 'success',
                                  message: '删除成功!'})
                              this.int()
                              this.bids=[]
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
              this.bids=[]

              val.forEach(e=>{
                  this.bids.push(e.bid)
              })
          },
        // 分页
          handleSizeChange(val) {
              this.pagee=val
              getAction('/kunze/brand/qryBrandList',{pageSize:val}).then((res)=>{
                  this.tableData=res.result.list

              })
          },
          handleCurrentChange(val) {
              getAction('/kunze/brand/qryBrandList',{pageSize:this.pagee,pageNo:val}).then((res)=>{
                  this.tableData=res.result.list

              })
          },
        // 点击取消添加按钮
          qxtj(){
              this.dialogFormVisible=false
              this.form.image=''
              this.form.name=''
              this.form.kid=''
              this.form.letter=''
              this.imageUrl=''
          },
        // 点击确定添加品按钮
          addpp(){

              this.form.image=this.imageUrl

              let brandVo=JSON.parse(JSON.stringify(this.form))

              postAction('/kunze/brand/saveBrand',{brandVo: brandVo}).then((res)=>{

                  if(res.code==200){
                      this.$message({
                          type: 'success',
                          message: '添加成功!'
                      });
                      this.form.image=''
                      this.form.name=''
                      this.form.kid=''
                      this.form.letter=''
                      this.dialogFormVisible=false
                      this.imageUrl=''
                  }else {
                      this.$message.error('添加失败');
                  }

              }).catch((err)=>{
                  this.$message.error('添加失败');
              })
          },
        //   图片上传
          handleAvatarSuccess(res, file) {
              this.imageUrl = URL.createObjectURL(file.raw);

          },
          beforeAvatarUpload(file) {
              const isJPG = file.type === 'image/jpeg';
              const isLt2M = file.size / 1024 / 1024 < 2;

              if (!isJPG) {
                  this.$message.error('上传头像图片只能是 JPG 格式!');
              }
              if (!isLt2M) {
                  this.$message.error('上传头像图片大小不能超过 2MB!');
              }
              return isJPG && isLt2M;
          },

        // 删除单个
          ppdel(row){
              this.$confirm('是否删除该品牌?', '提示', {
                  confirmButtonText: '确定',
                  cancelButtonText: '取消',
                  type: 'warning'
              }).then(() => {
                  let bid = row.bid
                  deleteAction('/kunze/brand/delBrand', {bid: bid}).then((res) => {
                      if(res.code==200){
                          this.$message({
                              type: 'success',
                              message: '删除成功!'
                          });
                          this.int()
                      }else {
                          this.$message.error('删除失败');
                      }

                  }).cache((err) => {
                      this.$message.error('删除失败');
                  })
              })
          },
        int(){
            getAction('/kunze/brand/qryBrandList',{pageSize:10}).then((res)=>{
               this.tableData=res.result.list

            })
            getAction('/kunze/brand/qryBrandList',{pageSize:1}).then((res)=>{


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
   /deep/.el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
   /deep/.el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
