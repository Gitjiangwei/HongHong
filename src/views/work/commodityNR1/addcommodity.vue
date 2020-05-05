<template>
<!--    新增商品-->
  <div class="xz-box">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>商品展示</span>
      </div>
    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
      <el-form-item label="商品分类" prop="cid3" >
          <el-cascader
            v-model="value"
            :options="nrr"
            @change="handleChange"
            placeholder="请选择商品分类">
          </el-cascader>
      </el-form-item>

      <template v-for="v in mmm">

          <el-form-item :label=v.k prop="cansu" v-if="v.options.length!=0">
            <template v-if="v.options.length==0">
<!--              <el-input  ></el-input>-->
            </template>
            <template v-else v-for="(j,m) in v.options" >
              <el-radio-group v-model="v.ridio" style="margin-right: 10px">
              <el-radio  :label="j" border @change="niuniu(v.k,j,m)">{{j}}</el-radio >
              </el-radio-group>
            </template>
          </el-form-item>

      </template>

      <el-form-item label="是否有效">
        <el-radio v-model="ruleForm.enable" label="1">是</el-radio>
        <el-radio v-model="ruleForm.enable" label="0">否</el-radio>
      </el-form-item>
      <el-form-item label="商品图片"  >


        <el-upload
          :action="urll"
        list-type="picture-card"
        :limit="imgLimit"
        :file-list="productImgs"
        :multiple="isMultiple"
        :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove"
        :on-success="handleAvatarSuccess"
        :before-upload="beforeAvatarUpload"
        :on-exceed="handleExceed"
        :on-error="imgUploadError">
        <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>


      </el-form-item>
      <el-form-item label="商品名称" prop="title">
        <el-input  v-model="ruleForm.title"></el-input>
      </el-form-item>

      <el-form-item label="子标题" prop="subTitle">
        <el-input  v-model="ruleForm.subTitle"></el-input>
      </el-form-item>
      <el-form-item label="商品价格" prop="price">
        <el-input  v-model="ruleForm.price" placeholder="价格默认为分"></el-input>
      </el-form-item>
      <el-form-item label="商品库存" prop="stock">
        <el-input  v-model="ruleForm.stock"></el-input>
      </el-form-item>
      <el-form-item label="售后服务" prop="afterService">
        <el-input  v-model="ruleForm.afterService"></el-input>
      </el-form-item>
      <el-form-item label="包装清单" prop="packingList">
        <el-input  v-model="ruleForm.packingList"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')" >立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
    </el-card>
  </div>
</template>

<script>

  import {getAction} from '../../../api/manage'
  import {postAction} from '../../../api/manage'
  import { getFileAccessHttpUrl } from '@/api/manage'
  import Vue from 'vue'
  export default {
    name: 'addcommodity',
      data(){
        return{
            nrr:[],
            ruleForm: {
                title:'',   //标题
                subTitle:'', //子标题
                brandId: '',   // 品牌id
                cid1: '',
                cid2: '',
                cid3: '',
                price:'',  //销售价格，单位为分
                enable:'1',  //是否有效，0无效，1有效
                ownSpec:'',   //sku的特有规格参数键值对
                indexes:[],   //属性下标
                stock:'',  //库存
                afterService:'', // 售后服务
                description:'', // 商品描述
                packingList:'', // 包装清单
                specTemplate:{},// 商品特殊规格的名称及可选值模板(不用手动填写)
                specifications:"",// 商品的全局规格属性 (不用手动填写)

            },
            rules: {
                cid3: [
                    { required: true, message: '请选择分类', trigger: 'change' },
                    // { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                // cansu: [
                //     { required: true, message: '请选择完整的商品参数', trigger: 'change' }
                // ],
                cimg: [
                    { type: 'date', required: true, message: '请上传图片', trigger: 'change' }
                ],
                title: [
                    { required: true, message: '请填写商品子标题', trigger: 'change' }
                ],
                subTitle: [
                    { required: true, message: '请填写商品子标题', trigger: 'change' }
                ],
                stock: [
                    { required: true, message: '请填写商品库存', trigger: 'blur' }
                ],
                afterService: [
                    { required: true, message: '请填写商品售后', trigger: 'blur' }
                ],
                price: [
                    { required: true, message: '请填写商品价格', trigger: 'blur' }
                ],
                packingList: [
                    { required: true, message: '请填写商品包装清单', trigger: 'blur' }
                ]
            },
            value: [],
            disabled: false,
            canshu:[],
            radio:[],
            mmm:[],
            csmb:{},
            // 图片上传
            dialogImageUrl: '',
            dialogVisible: false,
            productImgs: [],
            isMultiple: true,
            imgLimit: 6,
            urll:window._CONFIG['domianURL']+"/kunze/wheel/saveWheel"



        }
      },
      methods:{
        // 点击单选按钮之后
          niuniu(k,l,m){
              let nn=[]
              this.$set(this.csmb,k,l);
              this.ruleForm.ownSpec=this.csmb
                this.mmm.forEach(e=>{
                    if(e.options.length!=0){
                        nn.push(e.k)
                    }
                })
              nn.forEach((e,i)=>{
                  if(e==k){
                      this.ruleForm.indexes[i]=m
                  }
              })
              this.ruleForm.specifications.forEach(e=>{
                  e.params.forEach(j=>{
                      if(j.k==k){
                          j.options=l
                      }

                  })
              })


          },
        // 图片上传


          handleRemove(file, fileList) {//移除图片
              console.log(file, fileList);
          },
          handlePictureCardPreview(file) {//预览图片时调用
              console.log(file);
              this.dialogImageUrl = file.url;
              this.dialogVisible = true;
          },

          beforeAvatarUpload(file) {//文件上传之前调用做一些拦截限制
              console.log(file);
              const isJPG = true;
              // const isJPG = file.type === 'image/jpeg';
              const isLt2M = file.size / 1024 / 1024 < 2;

              // if (!isJPG) {
              //   this.$message.error('上传头像图片只能是 JPG 格式!');
              // }
              if (!isLt2M) {
                  this.$message.error('上传图片大小不能超过 2MB!');
              }
              return isJPG && isLt2M;
          },
          handleAvatarSuccess(res, file) {//图片上传成功
              console.log(res);
              console.log(file);
              this.imageUrl = URL.createObjectURL(file.raw);
          },
          handleExceed(files, fileList) {//图片上传超过数量限制
              this.$message.error('上传图片不能超过6张!');
              console.log(file, fileList);
          },
          imgUploadError(err, file, fileList){//图片上传失败调用
              console.log(err)
              this.$message.error('上传图片失败!');
          },




          // 级联下拉选中之后
          handleChange(value) {
             this.ruleForm.cid1=value[0]
             this.ruleForm.cid2=value[1]
             this.ruleForm.cid3=value[2]


              this.ruleForm.indexes=[]


              getAction('/kunze/spec/specList',{categoryId:value[2]}).then((res)=>{
                  this.mmm=[]
                  // this.canshu=[]
                  this.canshu=JSON.parse(res.result.specifications)
                   this.canshu.forEach(e=>{
                       e.params.forEach((j)=>{
                           this.mmm.push(j)
                       })
                   })
                  this.ruleForm.csmb={}
                  this.mmm.forEach((e,i)=>{
                      this.$set( this.mmm[i],'ridio',e.options[0])
                      if(e.options.length!=0){
                          this.$set(this.csmb,e.k,e.options[0])
                          this.ruleForm.indexes.push(0)
                      }
                  })
                  this.ruleForm.specTemplate={}
                this.ruleForm.specifications=JSON.parse(JSON.stringify(this.canshu))
                  this.ruleForm.specifications.forEach(e=>{
                      e.params.forEach(j=>{
                          if(j.options.length!=0){
                              this.$set(this.ruleForm.specTemplate,j.k,j.options)
                              j.options=j.options[0]
                          }

                      })
                  })
              })


          },

          submitForm(formName) {
              this.$refs[formName].validate((valid) => {
                  if (valid) {
                      let spuBo={}

                      this.$set(spuBo,'brandId',this.ruleForm.brandId)
                      this.$set(spuBo,'title',this.ruleForm.title)
                      this.$set(spuBo,'subTitle',this.ruleForm.subTitle)
                      this.$set(spuBo,'cid1',this.ruleForm.cid1)
                      this.$set(spuBo,'cid2',this.ruleForm.cid2)
                      this.$set(spuBo,'cid3',this.ruleForm.cid3)
                      this.$set(spuBo,'skuVos',[])
                      this.$set(spuBo.skuVos,0,{})
                      this.$set(spuBo,'spuDetail',[])
                      this.$set(spuBo.spuDetail,0,{})
                      this.$set(spuBo.skuVos[0],'enable',this.ruleForm.enable)
                      this.$set(spuBo.skuVos[0],'images',11111)
                      this.$set(spuBo.skuVos[0],'indexes',this.ruleForm.indexes)
                      this.$set(spuBo.skuVos[0],'ownSpec',this.ruleForm.ownSpec)
                      this.$set(spuBo.skuVos[0],'price',this.ruleForm.price)
                      this.$set(spuBo.skuVos[0],'stock',this.ruleForm.stock)
                      this.$set(spuBo.spuDetail[0],'afterService',this.ruleForm.afterService)
                      this.$set(spuBo.spuDetail[0],'description',this.ruleForm.description)
                      this.$set(spuBo.spuDetail[0],'packingList',this.ruleForm.packingList)
                      this.$set(spuBo.spuDetail[0],'specTemplate',this.ruleForm.specTemplate)
                      this.$set(spuBo.spuDetail[0],'specifications',this.ruleForm.specifications)

                      // spuBo.spuDetail=JSON.stringify(spuBo.spuDetail)
                      // let nn=JSON.stringify(spuBo)
                      let nn=JSON.parse(JSON.stringify(spuBo))

                      postAction('/kunze/spu/saveGood',{spuBo:nn}).then(res=>{
                          console.log(res);
                      })
                  } else {

                      return false;
                  }
              });
          },
          resetForm(formName) {
              this.$refs[formName].resetFields();
              this.mmm=[]

          },
          int(){
              // 商品分类查询
              getAction('/kunze/category/qryList', {id:'',pid:""}).then((res)=>{

                  // console.log(res)
                  res.result.forEach(e=>{
                      if(e.parentId==0){
                          this.nrr.push(e)
                      }
                  })

                  this.nrr.forEach(e=>{
                      // let children=[]

                      e.children=e.childrenList
                      e.label=e.name
                      e.value=e.id
                      e.childrenList.forEach(y=>{
                          y.children=y.childrenList
                          y.label=y.name
                          y.value=y.id
                          y.childrenList.forEach(x=>{
                              x.label=x.name
                             x.value=x.id
                          })
                      })
                  })



              })

          }

      },
      mounted() {
        this.int()

      }
  }
</script>

<style scoped>
  /deep/.el-input{
    width: 50%;
  }
  /deep/.el-input--suffix{
    width: 100% !important;
  }
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
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
  /deep/.el-upload{
    border: 1px dashed #d9d9d9;
  }
</style>
