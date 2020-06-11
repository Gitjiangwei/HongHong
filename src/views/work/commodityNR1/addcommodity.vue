<template>
<!--    新增商品-->
  <div>
  <a-card title="新增商品">
    <div style="width: 65%">
      <template>
        <a-steps :current="current" class="jdt">
          <a-step>
            <!-- <span slot="title">Finished</span> -->
            <template slot="title">
              基本信息
            </template>
          </a-step>
          <a-step>
            <template slot="title">
              参数规格
            </template>
          </a-step>
          <a-step>
            <template slot="title">
              商品描述
            </template>
          </a-step>
        </a-steps>
      </template>
<!--第一步-->
    <template v-if="current==0">
      <a-form
        :form="formTranslate"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
      >
        <a-form-item label="标题"  hasFeedback>
          <a-input  v-decorator="['title', {rules: [{ required: true, message: '请输入商品名称', }]}]" />
        </a-form-item>
        <a-form-item label="子标题" hasFeedback>
          <a-input  v-decorator="['subTitle', {rules: [{ required: true, message: '请输入子标题', }]}]" />
        </a-form-item>
        <a-form-item label="品牌" >
          <a-select v-model="form.brand" placeholder="选择品牌"  >
          <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys">
            {{v.bname}}
          </a-select-option>
        </a-select>
        </a-form-item>
      </a-form>
      <div style="margin-left: 35%">
        <a-button type="primary" disabled style="margin-right: 20px">
          上一步
        </a-button>
        <a-button type="primary" @click="nextStep" >
          下一步
        </a-button>
      </div>

    </template>

<!--第二步-->
      <template v-if="current==1">
        <a-form
          :form="formTranslate"
          :label-col="labelCol"
          :wrapper-col="wrapperCol"
        >
                <a-form-item label="分类" hasFeedback >
                  <a-input v-model="fenl"  type="hidden" />
                  <a-cascader
                    :field-names="{ label: 'name', value: 'id', children: 'childrenList' }"
                    :options="options"
                    placeholder="选择所属分类"
                    @change="onChange"
                  />
                </a-form-item>
                <template v-if="dxuandatas.length!=0">
                  <a-form-item label="选择参数" >
                    <template v-for="v in dxuandatas">
                      <template v-for="x in v.params">
                        <template v-if="x.options!=''">
                          <a-row>
                            <a-col :span="4">
                              <span class="fenzu">{{x.k}}:</span>
                            </a-col>
                            <a-col :span="20">
                              <a-radio-group  :name=x.k :v-model=x.value :options="x.options" :default-value="x.options[0]" @change="onChange2" />
                            </a-col>
                          </a-row>
                          <br />
                        </template>
                      </template>
                    </template>
                  </a-form-item>
                </template>
                <a-form-item label="销售价格" hasFeedback>
                  <a-input  placeholder="单位为元" v-decorator="['price', {rules: [{ required: true, message: '请输入销售价格', }]}]" />
                </a-form-item>
                <a-form-item label="商品库存"  hasFeedback>
                  <a-input   v-decorator="['stock', {rules: [{ required: true, message: '请输入商品库存', }]}]"  />
                </a-form-item>

        </a-form>
        <div style="margin-left: 35%">
          <a-button type="primary"  style="margin-right: 20px" @click="nextStep1">
            上一步
          </a-button>
          <a-button type="primary" @click="determine" style="margin-right: 20px">
            保存并再次添加
          </a-button>
          <a-button type="primary" @click="nextStep2">
            下一步
          </a-button>
        </div>

      </template>

      <!--第三步-->
      <template v-if="current==2">
        <a-form
          :model="form"
          :label-col="labelCol"
          :wrapper-col="wrapperCol"
        >
                <a-form-item label="商品图片" >
                  <j-image-upload class="avatar-uploader" text="上传" v-model="fileList" ></j-image-upload>
                </a-form-item>
                <a-form-item label="商品轮播图片" >
                  <j-image-upload class="avatar-uploader" text="上传" v-model="fileList1" style="width: 104px;margin-right: 30px;float: left"></j-image-upload>
                  <j-image-upload class="avatar-uploader" text="上传" v-model="fileList2" style="width: 104px;margin-right: 30px;float: left"></j-image-upload>
                  <j-image-upload class="avatar-uploader" text="上传" v-model="fileList3" style="width: 104px;float: left "></j-image-upload>
                </a-form-item>
                <a-form-item label="包装清单"  >
                  <a-input v-model="form.packingList" />
                </a-form-item>
                <a-form-item label="售后服务"   >
                  <a-input v-model="form.afterService" />
                </a-form-item>
                <a-form-item label="商品描述" >
                  <j-editor v-model="form.description"/>
                </a-form-item>

        </a-form>
        <div style="margin-left: 35%">
          <a-button type="primary"  style="margin-right: 20px" @click="nextStep3">
            上一步
          </a-button>
          <a-button type="primary" @click="onSubmit">
            确定添加
          </a-button>
        </div>

      </template>


    </div>
  </a-card>



  </div>
</template>

<script>
  import { getAction, httpAction, postAction } from '../../../api/manage'
  import JImageUpload from '../../../components/jeecg/JImageUpload'
  import JEditor from '@/components/jeecg/JEditor'

  import Vue from 'vue'
  import pick from 'lodash.pick'
  import moment from 'moment'
  export default {
    name: 'addcommodity',
    components: {
      JImageUpload,
      JEditor
    },
      data(){
        return{
          fenl:'',
          labelCol: { span: 4 },
          wrapperCol: { span: 14 },
          other: '',
          current:0,
          skuVos:[],
          shopId:"",
          form: {
            brand:'',
            price:"",
            stock:'',
            description:'',
            afterService:'',
            packingList:'',
            subTitle:'',
            title:'',
          },

          brand:[],  //品牌的集合
          options:[],  //分类级联下拉
          dxuandatas:[],  //参数集合
          ownSpec:{},
          indexes:[],
          index:[],
          specTemplate:{},
          specifications:[],
          fileList:[],
          fileList1:[],
          fileList2:[],
          fileList3:[],
          cids:[],
          formTranslate: this.$form.createForm(this),

        }
      },
      methods:{


        edit(record) {
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','brand','price','stock'))
          });

        },


      // 点击下一步
        nextStep(){



          let that = this
          // 触发表单验证
          this.formTranslate.validateFields((err, values) => {
            if(values.title && values.subTitle ){
              that.form.title=values.title
              that.form.subTitle=values.subTitle
              that.form.brand=values.brand
              this.current=this.current-(-1)
            }
          })


        },
        nextStep1(){
          this.current=this.current-1
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','brand','price','stock'))
          });
        },
        nextStep2(){


          this.formTranslate.validateFields((err, values) => {

            if (values.price && values.stock) {

              if (this.indexes.length != 0) {
                this.skuVos.push({
                  indexes: this.indexes,
                  ownSpec: this.ownSpec,
                  price: values.price,
                  stock: values.stock
                })
                this.current = this.current - (-1)
              } else {
                this.$message.warning('请选择所属分类');
              }
            }
          })



        },
        nextStep3(){
          this.current=this.current-1
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','brand','price','stock'))
          });
        },
        nextStep4(){
          // this.current=this.current-(-1)
        },
        determine(){

          this.formTranslate.validateFields((err, values) => {

            if(values.price && values.stock){

              if(this.indexes.length!=0){
                console.log(1)
                this.skuVos.push({
                  indexes: this.indexes,
                  ownSpec:  this.ownSpec,
                  price: values.price,
                  stock: values.stock
                })
                this.form.stock=''
                this.form.price=''
                this.dxuandatas=[]
                this.$message.success('保存成功，可以再次添加');
              }else {
                this.$message.warning('请选择所属分类');
              }

            }
          })

        },
        //点击参数单选框
        onChange2(e){
          let val=e.target.value
          let key=e.target.name
          let that=this
          for(let i in that.ownSpec){
            if(key==i){
              that.ownSpec[i]=val
            }
          }
          that.index.forEach((e,y)=>{
            if(e==key){
              for(let i in that.specTemplate){
                if(key==i){
                  that.specTemplate[i].forEach((e,l)=>{
                    if(e==val){
                      that.indexes[y]=l
                    }
                  })
                }
              }
              that.specifications.forEach(e=>{
                e.params.forEach(e=>{
                  if(e.k==key){
                    e.v=val
                  }
                })
              })
            }
          })

        },
        onChange(value){
          this.fenl=1
          this.cids=value
          let that=this
          that.indexes=[]
          that.ownSpec={}
          that.index=[]
          that.specTemplate={}
          that.specifications=[]
          getAction('/kunze/spec/specList',{categoryId:value[2]}).then((res)=>{
            if(res.result==null){
              that.dxuandatas=[]
            }else {
              that.dxuandatas=JSON.parse(res.result.specifications)
              that.dxuandatas.forEach(e=>{

                e.params.forEach((y,i)=>{
                  y.value=''

                  if(typeof y.options=='string'){
                    y.options=y.options.split(',');
                    if(y.options[0]==''){

                    }else {
                      // console.log(y.k,y.options[0])
                      Vue.set(that.ownSpec,y.k,y.options[0])
                      Vue.set(that.specTemplate,y.k,y.options)
                      that.indexes.push(0)
                      that.index.push(y.k)
                      let nn= {
                          k:y.k,
                          v:y.options[0],
                          global: true,
                          searchable: true
                        }
                        let nm=[]
                      nm.push(nn)
                      that.specifications.push({
                        group:e.group,
                        params:nm
                      })

                    }

                  }
                })

              })

            }

          })

        },

        onSubmit(){
          let that=this

              this.skuVos.forEach(e=>{
                e.indexes=JSON.stringify(e.indexes)
                e.ownSpec=JSON.stringify(e.ownSpec)
              })
              // fileList1:[],
              //   fileList2:[],
              //   fileList3:[],


              console.log(this.skuVos)
              let spuBo={
                'brandId': this.form.brand,
                'cid1': this.cids[0],
                'cid2': this.cids[1],
                'cid3': this.cids[2],
                'image': this.fileList,
                'images': this.fileList1+","+this.fileList2+","+this.fileList3,
                'shopId':this.shopId,
                'skuVos':this.skuVos,
                'spuDetail': {
                  'afterService': this.form.afterService,
                  'description': this.form.description,
                  'packingList': this.form.packingList,
                  'specTemplate': JSON.stringify(this.specTemplate),
                  'specifications':JSON.stringify(this.specifications),
                },
                'subTitle': this.form.subTitle,
                'title':this.form.title
              }

              // console.log(spuBo)
              httpAction('/kunze/spu/saveGood', spuBo,'post').then((res)=>{
                console.log(res)
                if(res.success==true){
                  that.current=0
                  that.form.brand=''
                  that.cids=[]
                  that.fileList=[]
                  that.skuVos=[]
                  that.form.afterService=''
                  that.form.description=''
                  that.form.price=''
                  that.form.stock=''
                  that.form.packingList=''
                  that.specTemplate=[]
                  that.specifications=[]
                  that.indexes=[]
                  that.form.subTitle=''
                  that.form.title=''
                  that.$message.success('添加成功');
                }else {
                  that.$message.warning('添加失败');
                  // console.log(res)
                }
              })




        },
        resetForm(){},
        getBrand(){
          let that=this
          getAction('/kunze/brand/qryBrandList',{pageSize:1}).then((res)=>{
            getAction('/kunze/brand/qryBrandList',{pageSize:res.result.pages}).then((res)=>{
              let key=0
              res.result.list.forEach(e=>{
                e. keys=key++
              })
              that.brand=res.result.list

            })
          })
          getAction('/kunze/category/qryList',{id:'',pid:''}).then((res)=>{
            that.options=res.result
          })
        },
      },
      mounted() {
      // if(this.$store.state.shopId!=null){
      //
      // }else if(JSON.parse(sessionStorage.getItem("store")).shopId!=nill){
      //
      // }
      //   this.shopId=this.$store.state.shopId
        this.shopId=JSON.parse(sessionStorage.getItem("store")).shopId
        // this.$message.success(this.shopId);
        // console.log(this.shopId)
      this.getBrand()
      }
  }
</script>

<style scoped>
  .fenzu{
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 1;
    overflow: hidden;
  }
  .avatar-uploader > .ant-upload {
    width:104px;
    height:104px;
  }
  .jdt{
    margin-bottom: 20px;
  }
</style>
