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
              <a-select-option value=''>全部</a-select-option>
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
    <a-button type="primary" class="modifyBtn1" @click="updataAllsable()">批量上架</a-button>
    <a-button type="primary" class="modifyBtn1" @click="updataAllsablexia()">批量下架</a-button>
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
          <a-button type="primary" class="modifyBtn"  @click="shangjia(record)" v-if="record.saleable==1">已上架</a-button>
          <a-button type="danger" class="modifyBtn" @click="shangjia(record)" v-if="record.saleable==0">已下架</a-button>
      </span>
      <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="xiuBrandBtn(record)">查看</a-button>
          <a-button type="primary" @click="deleteBrandBtn(record)">删除</a-button>
      </span>
    </a-table>
  </a-card>



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
          <a-form-item label="品牌" hasFeedback>
            <a-select  placeholder="选择品牌"   v-decorator="['brand', {rules: [{ required: true, message: '请选择品牌', }]}]">
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
                  <template v-if="x.options!='' && x.global=='false'">
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
          <a-form-item label="销售价格"  hasFeedback>
            <a-input  placeholder="单位为元" v-decorator="['price', {rules: [{ required: true, message: '请输入销售价格', }]}]" />
          </a-form-item>
          <a-form-item label="商品库存"   hasFeedback>
            <a-input  v-decorator="['stock', {rules: [{ required: true, message: '请输入商品库存', }]}]" />
          </a-form-item>
          <a-form-item label="商品图片" hasFeedback>
            <j-image-upload class="avatar-uploader" text="上传"  v-decorator="['skuimage', {rules: [{ required: true, message: '请输入商品库存', }]}]" ></j-image-upload>
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
          <a-form-item label="商品图片" hasFeedback>
            <j-image-upload class="avatar-uploader" text="上传"   v-decorator="['spuimage', {rules: [{ required: false, message: '请输入商品库存', }]}]"></j-image-upload>
          </a-form-item>
          <a-form-item label="商品详情轮播图" hasFeedback>
            <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage1', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
            <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage2', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
            <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage3', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
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
            确定修改
          </a-button>
        </div>
      </template>
    </a-drawer>
<!--    删除商品提示框-->
    <a-modal
      title="删除"
      :visible="delspuvisible"
      @ok="delspuhandleOk"
      @cancel="delspuhandleCancel"
    >
      <p>确定要删除该商品吗</p>
    </a-modal>
  </div>
</template>
<script>
  import {getAction, httpAction, postAction} from '@/api/manage'
  import JImageUpload from '../../../components/jeecg/JImageUpload'
  import JEditor from '@/components/jeecg/JEditor'
  import Vue from 'vue'
  import pick from 'lodash.pick'
  import qs from 'qs'

  export default {
    components: {
      JImageUpload,
      JEditor
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
            skuimage:"",
            spuimage:'',
            spuimage1:"",
            spuimage2:"",
            spuimage3:""
          },
          skuVos:[],
          formTranslate: this.$form.createForm(this),
          xiuBrandvisible:false,
          delspuvisible:false,
          visible:false,
          id:'',
          xiushopForm:{},
          // fileList:[],
          indexes:[],
          index:[],
          specifications:[],
          ownSpec:{},
          spu:{},
          sku:{},
          options:[],
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
        delspuhandleCancel(){
          this.delspuvisible=false
        },
        delspuhandleOk(){
          this.delspuvisible=false
          postAction('/kunze/spu/deleteSpu',this.ids).then((res)=>{
            if(res.success==true){
              this.getAllProducts(this.shopId)
              this.$message.success('删除商品成功');
              this.ids=[]
            }else {
              this.getAllProducts(this.shopId)
              this.$message.error('删除商品失败');
              this.ids=[]
            }
          })
        },
      //批量上架
        updataAllsable(){
            let  data = {
              saleable:'1',
              shopId:this.shopId,
              spuList:this.ids
            }
          postAction('/kunze/spu/updateSpuSaleable',data).then((res)=>{
              if(res.success==true){
                this.getAllProducts(this.shopId)
                this.$message.success('上架成功');
              }else {
                this.$message.warning('上架失败 ');
              }
            })
        },
        //批量下架
        updataAllsablexia(){
            let  data = {
              saleable:'0',
              shopId:this.shopId,
              spuList:this.ids
            }
            postAction('/kunze/spu/updateSpuSaleable',data).then((res)=>{
              // console.log(res)
              if(res.success==true){
                this.getAllProducts(this.shopId)
                this.$message.success('下架成功');
              }else {
                this.$message.warning('下架失败 ');
              }
            })
        },
      //选择分类
        onChangeShop(e){
          this.cids=e
          this.search.cid3=e[2]
        },
      //点击搜索商品
        searchShop(){
          let that=this
          getAction('/kunze/spu/spuList',{
            pageNo :that.ipagination.current,
            pageSize : that.ipagination.pageSize,
            shopId : that.shopId,
            brandId:that.search.brandId,
            cid3:that.search.cid3,
            title:that.search.title,
            saleable:that.search.saleable,
            id:that.search.shopId
          }).then((res)=>{
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
          let spuList=[]
          spuList.push(e.id)
          if(e.saleable==0){
            let  data = {
              saleable:'1',
              shopId:this.shopId,
              spuList:spuList
            }
            postAction('/kunze/spu/updateSpuSaleable',data).then((res)=>{
              if(res.success==true){
                this.getAllProducts(this.shopId)
                this.$message.success('上架成功');
              }else {
                this.$message.warning('上架失败 ');
              }
            })
          }else {
            let  data = {
              saleable:'0',
              shopId:this.shopId,
              spuList:spuList
            }
            postAction('/kunze/spu/updateSpuSaleable',data).then((res)=>{
              if(res.success==true){
                this.getAllProducts(this.shopId)
                this.$message.success('下架成功');
              }else {
                this.$message.warning('下架失败 ');
              }
            })
          }
        },
        //分页
        handleTableChange(pagination, filters, sorter) {
          //分页、排序、筛选变化时触发
          //TODO 筛选
          if (Object.keys(sorter).length > 0) {
            this.isorter.column = sorter.field;
            this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
          }
          this.ipagination = pagination;
          this.getAllProducts(this.shopId);
        },
        xiuskuBrandBtn(e){
          this.sku=e
          let that=this
          this.cids.push(e.cid1)
          this.cids.push(e.cid2)
          this.cids.push(e.cid3)
          this.form.skuimage=e.skuimage
          this.form.spuimage1=this.spu.images[0]
          this.form.spuimage2=this.spu.images[1]
          this.form.spuimage3=this.spu.images[2]
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
          })
          this.xiuBrandvisible=true
          this.brand.forEach(e=>{
            if(e.bname==this.spu.bname){
              this.form.brand=e.bid
            }
          })
          this.indexes=e.indexes.split(',')
          this.form.title=this.spu.title
          this.form.subTitle=this.spu.subTitle
          this.form.price=e.price
          this.form.stock=e.stock
          this.form.packingList= e.packingList
          this.form.afterService=e.afterService
          this.form.description=e.description
          this.form.spuimage= this.spu.spuimage
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title','brand', 'subTitle','price','stock','brand','skuimage','spuimage','spuimage1','spuimage2','spuimage3'))
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
          this.delspuvisible=true
          this.ids=[]
          this.ids.push(e.id)

        },
        //批量删除
        deleteAllBrandBtn(){
          this.delspuvisible=true
          // console.log(this.ids)
        },
        //点击查看按钮
        xiuBrandBtn(e){
          // this.form.banimage1=e.images
          this.spu=[]
          this.skudata=[]
          let that=this
          let param = new URLSearchParams()
          param.append('spuId',e.id)
          postAction('/kunze/sku/qrySkuBySpuId',param).then((res)=>{
            console.log(res)
            that.skudata=res.result
            that.skudata.forEach(e=>{
              e.skuimage=e.images
              e.images=window._CONFIG['domianURL']+'/'+e.images
              e.ownSpec=e.ownSpec.slice(1,e.ownSpec.length-1)
            })
          })
          this.visible=true
          that.spu=e
          that.spu.images=that.spu.images.split(",")


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
            // console.log(res)
            that.data=res.result.list

            let key=0
            that.data.forEach(e=>{
              e.spuimage=e.image
              e.image=window._CONFIG['domianURL']+'/'+e.image

              e.key=key++
            })
            that.ipagination.total = res.result.total;
            // console.log(that.data)
          })
        },
        edit(record) {
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock','skuimage','spuimage','spuimage1','spuimage2','spuimage3'))
          });
        },
        // 点击下一步
        nextStep(){
          let that = this
          that.onChange(that.cids)
          // 触发表单验证
          this.formTranslate.validateFields((err, values) => {
            console.log(values)
            if(values.title && values.subTitle && values.brand){
              that.form.title=values.title
              that.form.subTitle=values.subTitle
              that.form.brand=values.brand
              this.current=this.current-(-1)
              this.$nextTick(() => {
                this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock','brand','skuimage','spuimage','spuimage1','spuimage2','spuimage3'))
              });
            }
          })
        },
        nextStep1(){
          this.current=this.current-1
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock','brand','skuimage','spuimage','spuimage1','spuimage2','spuimage3'))
          });
        },
        nextStep2(){
          let that = this
          // 触发表单验证
          this.formTranslate.validateFields((err, values) => {
            if(values.price && values.stock && values.skuimage){
              that.form.stock=values.stock
              that.form.price=values.price
              that.form.skuimage=values.skuimage
              if(this.indexes.length!=0){
                this.skuVos.push({
                  id: that.sku.id,
                  indexes: this.indexes,
                  ownSpec:  this.ownSpec,
                  price: values.price,
                  stock: values.stock,
                  images:values.skuimage

                })
                console.log(this.skuVos)
                this.current=this.current-(-1)
              }else {
                this.$message.warning('请选择所属分类');
              }
            }
          })
        },
        nextStep3(){
          this.current=this.current-1
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','stock','skuimage','spuimage','spuimage1','spuimage2','spuimage3'))
          });
        },
        determine(){
          this.formTranslate.validateFields((err, values) => {
            let that = this
            if(values.price && values.stock && values.skuimage){
              that.form.title=values.stock
              that.form.subTitle=values.price
              that.form.skuimage=values.skuimage
                if (this.indexes.length != 0) {
                  that.skuVos.push({
                    id: that.sku.id,
                    indexes: that.indexes,
                    ownSpec: that.ownSpec,
                    price: values.price,
                    stock: values.stock,
                    images:values.skuimage.substr(38)
                  })
                  this.form.stock = ''
                  this.form.price = ''
                  this.dxuandatas = []
                } else {
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
                      if(y.global=='false') {
                        Vue.set(that.ownSpec, y.k, y.options[0])
                      }
                      if(y.global=='false'){
                        Vue.set(that.specTemplate,y.k,y.options)
                      }
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
                      if(y.global=='true'){
                        that.specifications.push({
                          group:e.group,
                          params:nm
                        })
                      }
                    }
                  }
                })
              })
            }
          })
        },
        onSubmit(){
          let that=this
          this.formTranslate.validateFields((err, values) => {
                that.form.spuimage=values.spuimage
                that.form.spuimage1=values.spuimage1
                that.form.spuimage2=values.spuimage2
                that.form.spuimage3=values.spuimage3

              })
                this.skuVos.forEach(e => {
                  e.indexes = JSON.stringify(e.indexes)
                  e.ownSpec = JSON.stringify(e.ownSpec)
                })
                let spuBo = {
                  'brandId': this.form.brand,
                  'cid1': this.cids[0],
                  'cid2': this.cids[1],
                  'cid3': this.cids[2],
                  'image': this.form.spuimage,
                  "images": that.form.spuimage1 + ',' + that.form.spuimage2 + ',' + that.form.spuimage3,
                  'shopId': this.shopId,
                  "id": this.spu.id,
                  'skuVos': this.skuVos,
                  'spuDetail': {
                    'afterService': this.form.afterService,
                    'description': this.form.description,
                    'packingList': this.form.packingList,
                    'specTemplate': JSON.stringify(this.specTemplate),
                    'specifications': JSON.stringify(this.specifications),
                  },
                  'subTitle': this.form.subTitle,
                  'title': this.form.title
                }
                httpAction('/kunze/spu/updateSpu', spuBo, 'post').then((res) => {
                  if (res.success == true) {
                    that.current = 0
                    that.form.brand = []
                    that.cids = []
                    this.form.spuimage = ""
                    that.skuVos = []
                    that.form.afterService = ''
                    that.form.description = ''
                    that.form.price = ''
                    that.form.stock = ''
                    that.form.packingList = ''
                    that.specTemplate = []
                    that.specifications = []
                    that.indexes = []
                    that.form.subTitle = ''
                    that.form.title = ''
                    that.xiuBrandvisible = false
                    that.visible = false
                    that.$message.success('修改成功');
                    this.getAllProducts(this.shopId)
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
        if(JSON.parse(sessionStorage.getItem("store")).shopId){
          this.shopId=JSON.parse(sessionStorage.getItem("store")).shopId
        }else {
          this.shopId=sessionStorage.getItem('shopId')
        }
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
