<template>
<!--    商品展示-->
  <div>
  <a-card title="商品展示" >
    <a-form :label-col="{ span: 5 }" :wrapper-col="{ span: 19 }" >
      <a-row>
        <a-col :span="6">
          <a-form-item label="商品ID">
            <a-input placeholder="商品ID" v-model="search.shopId"></a-input>
          </a-form-item>
        </a-col>
        <a-col :span="6">
          <a-form-item label="商品名称">
            <a-input placeholder="商品名称" v-model="search.title"></a-input>
          </a-form-item>
        </a-col>
        <a-col :span="6">
          <a-form-item label="是否上架">
            <a-select v-model="search.saleable"  >
              <a-select-option value='1'>已上架</a-select-option>
              <a-select-option value='0'>已下架</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row>
        <a-col :span="6">
          <a-form-item label="品牌" prop="brand" >
            <a-select v-model="search.brandId" placeholder="选择品牌" :default-value=form.brand >
              <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys" >
                {{v.bname}}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
        <a-col :span="6">
          <a-form-item label="商品分类">
            <a-cascader
              :field-names="{ label: 'name', value: 'id', children: 'childrenList'}"
              :options="options"
              placeholder="选择所属分类"
              :value="cids"
              @change="onChangeShop"
            />
          </a-form-item>
        </a-col>
        <a-col :span="2">
        </a-col>
        <a-col :span="4">
        <a-button type="primary" icon="search" @click="searchShop">
          搜索
        </a-button>
        </a-col>
      </a-row>
    </a-form>


    <a-button type="primary" class="modifyBtn1" @click="deleteAllBrandBtn()">批量删除</a-button>
    <a-table
      :columns="columns"
      :data-source="data"
      :row-selection="{  onChange: onSelectChange }"
      :pagination="ipagination"

      @change="handleTableChange">
         <span slot="image" slot-scope="text,record">
           <img :src=record.image alt="">
        </span>
      <span slot="saleable" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn"  @click="shangjia(record)" v-if="record.saleable==1">下架</a-button>
          <a-button type="danger" class="modifyBtn" @click="shangjia(record)" v-if="record.saleable==0">上架</a-button>
      </span>
      <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="xiuBrandBtn(record)">查看</a-button>
          <a-button type="primary" @click="deleteBrandBtn(record)">删除</a-button>
      </span>
    </a-table>
  </a-card>



<!--        修改商品弹出框-->
    <a-drawer
      title="修改商品"
      :width="720"
      :visible="xiuBrandvisible"
      :body-style="{ paddingBottom: '80px' }"
      @close="xiushop"
    >
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
            <a-input v-decorator="['title', {rules: [{ required: true, message: '请输入商品名称', }]}]"  />
          </a-form-item>
          <a-form-item label="子标题" hasFeedback>
            <a-input    v-decorator="['subTitle', {rules: [{ required: true, message: '请输入子标题', }]}]" />
          </a-form-item>
          <a-form-item label="品牌">
            <a-select v-model="form.brand" placeholder="选择品牌" :default-value=form.brand >
              <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys" >
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
          <a-form-item label="分类" >
            <a-input v-model="fenl"  type="hidden" />
            <a-cascader
              :field-names="{ label: 'name', value: 'id', children: 'childrenList'}"
              :options="options"
              placeholder="选择所属分类"
              :value="cids"
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
          <a-form-item label="销售价格" prop="price">
            <a-input v-model="form.price" placeholder="单位为分" />
          </a-form-item>
          <a-form-item label="商品库存"  prop="stock">
            <a-input v-model="form.stock" />
          </a-form-item>

        </a-form>
        <div style="margin-left: 35%">
          <a-button type="primary"  style="margin-right: 20px" @click="nextStep1">
            上一步
          </a-button>
          <a-button type="primary" @click="determine" style="margin-right: 20px">
            确定参数
          </a-button>
          <a-button type="primary" @click="nextStep2">
            下一步
          </a-button>
        </div>

      </template>

      <!--第三步-->
      <template v-if="current==2">
        <a-form
          :form="formTranslate"
          :label-col="labelCol"
          :wrapper-col="wrapperCol"
        >
          <a-form-item label="商品图片" >
            <j-image-upload class="avatar-uploader" text="上传" v-model="fileList" ></j-image-upload>
          </a-form-item>
          <a-form-item label="包装清单" prop="packingList" >
            <a-input v-model="form.packingList" />
          </a-form-item>
          <a-form-item label="售后服务"  prop="afterService"  >
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
    </a-drawer>


<!--    sku弹出窗-->
    <a-modal
      v-model="visible"
      title="商品"
      @ok="handleOk"
      @cancel="handleCancel"
      width="60%">
        <a-table :columns="skucolumns" :data-source="skudata"  >
         <span slot="images" slot-scope="text,record">
           <img :src=record.images alt="">
        </span>
          <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="xiuskuBrandBtn(record)">修改</a-button>
          <a-button type="primary" @click="deleteskuBrandBtn(record)">删除</a-button>
        </span>
        </a-table>

    </a-modal>

  </div>
</template>

<script>
  import { getAction, httpAction, postAction } from '../../../api/manage'
  import JImageUpload from '../../../components/jeecg/JImageUpload'
  import Vue from 'vue'
  import pick from 'lodash.pick'



  export default {

    components: {
      JImageUpload
    },
    name: 'recycleBin',
      data(){
        return{
          columns:[
            { title: '*', dataIndex: '',  key: 'rowIndex', width: 60, align: "center", customRender: function (t, r, index) {return parseInt(index) + 1;}},
            { title: '品牌名称', dataIndex: 'bname', key: 'bname' },
            { title: '图片', dataIndex: 'image', key: 'image',scopedSlots: { customRender: 'image' } },
            { title: '所属分类', dataIndex: 'cname', key: 'cname' },
            { title: '商品名称', dataIndex: 'title', key: 'title' },
            { title: '是否上架', dataIndex: 'saleable', key: 'saleable', scopedSlots: { customRender: 'saleable' } },
            { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
          ],
          skucolumns:[
            { title: '商品名称', dataIndex: 'title', key: 'title' },
            { title: '规格', dataIndex: 'ownSpec', key: 'ownSpec' },
            { title: '图片', dataIndex: 'images', key: 'images',scopedSlots: { customRender: 'images' } },
            { title: '价格', dataIndex: 'price', key: 'price' },
            { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
          ],
          data:[],
          skudata:[],
          search:{
            shopId:"",
            title:'',
            brandId:'',
            cid3:'',
            saleable:""
          },
          shopId:'',
          ids:[],
          current:0,
          fenl:'',
          labelCol: { span: 4 },
          wrapperCol: { span: 14 },
          dxuandatas:[],
          brand:[],
          cids:[],
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
          formTranslate: this.$form.createForm(this),
          xiuBrandvisible:false,
          visible:false,
          id:'',
          xiushopForm:{},
          fileList:[],
          indexes:[],
          index:[],
          specifications:[],
          ownSpec:{},
          spu:{},
          ipagination:{
            current: 1,
            pageSize: 30,
            pageSizeOptions: ['30', '40', '50'],
            showTotal: (total, range) => {
              return range[0] + "-" + range[1] + " 共" + total + "条"
            },
            showQuickJumper: true,
            showSizeChanger: true,
            total: 0
          }
        }
      },
      methods:{
      //选择分类
        onChangeShop(e){
          console.log(e)
          this.cids=e
          this.search.cid3=e[2]
        },
      //点击搜索商品
        searchShop(){


          let that=this
          // console.log(that.shopId)
          getAction('/kunze/spu/spuList',{
            pageNo :that.ipagination.current,
            pageSize : that.ipagination.pageSize,
            shopId : that.shopId,
            brandId:that.search.brandId,
            cid3:that.search.cid3,
            subTitle:that.search.subTitle,
            saleable:that.search.saleable,
            id:that.search.shopId
          }).then((res)=>{
            console.log(res)
            that.data=res.result.list

            let key=0
            that.data.forEach(e=>{
              e.image=window._CONFIG['domianURL']+'/'+e.image
              e.key=key++
            })
            that.ipagination.total = res.result.total;
          })



        },
      //点击商品上架或下架
        shangjia(e){





        },




        //分页
        handleTableChange(pagination, filters, sorter) {
          //分页、排序、筛选变化时触发
          console.log(sorter);
          //TODO 筛选
          if (Object.keys(sorter).length > 0) {
            this.isorter.column = sorter.field;
            this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
          }
          this.ipagination = pagination;
          this.getAllProducts(this.shopId);
        },
        xiuskuBrandBtn(e){
          // console.log(e)
          let that=this
          this.cids.push(e.cid1)
          this.cids.push(e.cid2)
          this.cids.push(e.cid3)
          // console.log(e.cid3)
          getAction('/kunze/spec/specList',{categoryId:e.cid3}).then((res)=>{
            if(res.result==null){
              that.dxuandatas=[]
            }else {

              that.dxuandatas=JSON.parse(res.result.specifications)
              that.dxuandatas.forEach(e=>{
                e.params.forEach((y,i)=>{
                  y.value=''
                  y.options=y.options.split(',');
                  if(typeof y.options=='string'){
                    y.options=y.options.split(',');
                    if(y.options[0]==''){

                    }else {
                      // console.log(y.options,11111)
                      Vue.set(that.ownSpec,y.k,y.options[0])
                      // Vue.set(e.params[i],'opens',0)
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
            console.log(this.dxuandatas)

          })


          this.xiuBrandvisible=true
          console.log(e)

          this.indexes=e.indexes.split(',')
          this.form.title=this.spu.title
          this.form.subTitle=this.spu.subTitle
          this.form.brand=this.spu.bname
          this.form.price=e.newPrice
          this.form.stock=e.stock



          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock'))
          });





        },
        deleteskuBrandBtn(e){},
        handleCancel(){
          this.visible=false
        },
        handleOk(){
          this.visible=false
        },
        xiuBrandConfirm(){
          this.xiuBrandvisible=false
        },
        xiuBrandClose(){
          this.xiuBrandvisible=false
        },
        xiushop(){
          this.xiuBrandvisible=false
        },
        deleteBrandBtn(e){
          console.log(e)
        },
        //批量删除
        deleteAllBrandBtn(){

        },
        //点击查看按钮
        xiuBrandBtn(e){
          this.spu=[]
          this.skudata=[]
          let that=this
          let param = new URLSearchParams()
          param.append('spuId',e.id)
          postAction('/kunze/sku/qrySkuBySpuId',param).then((res)=>{
            that.skudata=res.result
            that.skudata.forEach(e=>{
              e.images=window._CONFIG['domianURL']+'/'+e.images
              e.ownSpec=e.ownSpec.slice(1,e.ownSpec.length-1)
            })
          })
          this.visible=true
          that.spu=e
        },
        onSelectChange(selectedRowKeys, selectedRows){
          let that=this
          selectedRows.forEach(e=>{
            that.ids.push(e.id)
          })
        },

        //获取所有商品
        getAllProducts(e){

          let that=this
          // console.log(that.shopId)
          getAction('/kunze/spu/spuList',{
            pageNo :that.ipagination.current,
            pageSize : that.ipagination.pageSize,
            shopId : e
          }).then((res)=>{
            console.log(res)
            that.data=res.result.list

            let key=0
            that.data.forEach(e=>{
              e.image=window._CONFIG['domianURL']+'/'+e.image
              e.key=key++
            })
            that.ipagination.total = res.result.total;
            console.log(that.data)
          })



        },

        edit(record) {
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock'))
          });

        },

        // 点击下一步
        nextStep(){
          let that = this
          // 触发表单验证
          this.formTranslate.validateFields((err, values) => {
            console.log(values)
            if(values.title && values.subTitle){
              that.form.title=values.title
              that.form.subTitle=values.subTitle
              this.current=this.current-(-1)
            }
          })
        },
        nextStep1(){
          this.current=this.current-1
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock'))
          });
        },
        nextStep2(){
          // this.current=this.current-(-1)
          this.$refs.ruleForm.validate(valid => {
            if (valid) {
              // alert('submit!');
              if(this.indexes.length!=0){
                this.skuVos.push({
                  indexes: this.indexes,
                  ownSpec:  this.ownSpec,
                  price: this.form.price,
                  stock: this.form.stock
                })
                this.current=this.current-(-1)
              }else {
                this.$message.warning('请选择所属分类');
              }


            } else {

              return false;
            }
          })
        },
        nextStep3(){
          this.current=this.current-1
        },
        nextStep4(){
          // this.current=this.current-(-1)
        },
        determine(){
          this.$refs.ruleForm.validate(valid => {
            if (valid) {
              if(this.indexes.length!=0){
                this.skuVos.push({
                  indexes: this.indexes,
                  ownSpec:  this.ownSpec,
                  price: this.form.price,
                  stock: this.form.stock
                })
                this.form.stock=''
                this.form.price=''
                this.dxuandatas=[]
              }else {
                this.$message.warning('请选择所属分类');
              }

            } else {

              return false;
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

          this.$refs.ruleForm.validate(valid => {
            if (valid) {
              this.skuVos.forEach(e=>{
                e.indexes=JSON.stringify(e.indexes)
                e.ownSpec=JSON.stringify(e.ownSpec)
              })


              let spuBo={
                'brandId': this.form.brand,
                'cid1': this.cids[0],
                'cid2': this.cids[1],
                'cid3': this.cids[2],
                'image': this.fileList,
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


              httpAction('/kunze/spu/saveGood', spuBo,'post').then((res)=>{
                if(res.success==true){
                  that.current=0
                  that.form.brand=[]
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
                }

              })
            } else {

              return false;
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
            console.log(res,321321321)
            that.options=res.result

          })
        },
      },
      mounted() {
        this.shopId=JSON.parse(sessionStorage.getItem("store")).shopId
      this.getAllProducts(this.shopId)

        this.getBrand()
      }
  }
</script>

<style scoped>
  .modifyBtn{
    margin-right: 10px;
  }
  .modifyBtn1{
    margin-bottom: 10px;
    margin-right: 10px;
  }
  img{
    width: 50px;
    height: 50px;
  }
</style>
