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

      <a-tabs default-active-key="1">
        <a-tab-pane key="1" tab="基本信息">
          <a-form
            :form="formTranslate"
            :label-col="labelCol"
            :wrapper-col="wrapperCol"
          >
            <a-row>
              <a-col :span="12">
                <a-form-item label="标题"  hasFeedback>
                  <a-input v-decorator="['title', {rules: [{ required: true, message: '请输入商品名称', }]}]"  />
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item label="子标题" hasFeedback>
                  <a-input    v-decorator="['subTitle', {rules: [{ required: true, message: '请输入子标题', }]}]" />
                </a-form-item>
              </a-col>
            </a-row>
            <a-row>
              <a-col :span="12">
                <a-form-item label="品牌" hasFeedback>
                  <a-select  placeholder="选择品牌"   v-decorator="['brand', {rules: [{ required: true, message: '请选择品牌', }]}]">
                    <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys" >
                      {{v.bname}}
                    </a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item label="售后服务"  hasFeedback  >
                  <a-input  v-decorator="['afterService', {rules: [{ required: false, message: '请输入售后服务', }]}]"/>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row>
              <a-col :span="12">
                <a-form-item label="包装清单" hasFeedback >
                  <a-input  v-decorator="['packingList', {rules: [{ required: false, message: '请输入包装清单', }]}]"/>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row>
              <a-col :span="12">
                <a-form-item label="spu轮播图" hasFeedback>
                  <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage1', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
                  <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage2', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
                  <j-image-upload class="avatar-uploader" text="上传" style="float: left"  v-decorator="['spuimage3', {rules: [{ required: false, message: '请输入商品库存', }]}]" ></j-image-upload>
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item label="商品图片" hasFeedback>
                  <j-image-upload class="avatar-uploader" text="上传"   v-decorator="['spuimage', {rules: [{ required: false, message: '请输入商品库存', }]}]"></j-image-upload>
                </a-form-item>
              </a-col>
            </a-row>

            <a-row>
              <a-col :span="24">
                <a-form-item label="商品描述" hasFeedback>
                  <j-editor  v-decorator="['description', {rules: [{ required: false, message: '请输入商品描述', }]}]"/>
                </a-form-item>
              </a-col>
            </a-row>

          </a-form>
          <a-button type="primary" @click="modifyBasic" style="margin-left:70%">确定修改基本信息</a-button>
        </a-tab-pane>




        <a-tab-pane key="2"  tab="规格">
          <a-table :columns="skucolumns" :data-source="skudata"  >
         <span slot="images" slot-scope="text,record">
           <img :src=record.images alt="">
        </span>
            <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="xiuskuBrandBtn(record)">修改</a-button>
          <a-button type="primary" @click="deleteskuBrandBtn(record)">删除</a-button>
        </span>
          </a-table>
        </a-tab-pane>
      </a-tabs>


    </a-modal>
<!--        修改商品弹出框-->

      <a-modal
        v-model="xiuBrandvisible"
        title="商品"
        @ok="nextStep2"
        @cancel="xiuhandleCancel"
        width="60%">


        <a-form
          :form="formTranslate"
          :label-col="labelCol"
          :wrapper-col="wrapperCol"
        >
          <a-form-item label="销售价格"  hasFeedback>
            <a-input  placeholder="单位为元"  v-decorator="['price', {rules: [{ required: true, message: '请输入商品价格', }]}]"  />
<!--            v-decorator="['price', validatorRules.spuPrice]"-->
          </a-form-item>
          <a-form-item label="优惠价格"  hasFeedback>
            <a-input  placeholder="单位为元"  v-decorator="['newprice', {rules: [{ required: true, message: '请输入优惠价格', }]}]"  />
            <!--            v-decorator="['price', validatorRules.spuPrice]"-->
          </a-form-item>
          <a-form-item label="商品库存"    hasFeedback>
            <a-input type="number"  v-decorator="['stock', {rules: [{ required: true, message: '请输入商品库存', }]}]" />
<!--            v-decorator="['stock', validatorRules.Stock]"-->
          </a-form-item>
          <a-form-item label="商品图片" hasFeedback>
            <j-image-upload class="avatar-uploader" text="上传"  v-decorator="['skuimage', {rules: [{ required: true, message: '请输入商品图片', }]}]" ></j-image-upload>
          </a-form-item>
        </a-form>
      </a-modal>
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

          fenl:'',
          labelCol: { span: 4 },
          wrapperCol: { span: 14 },
          dxuandatas:[],
          brand:[],
          cids:[],
          form: {
            brand:'',
            price:"",
            newprice:'',
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
          },
          validatorRules:{
            spuPrice: {
              rules: [{required: true,validator: this.checkSpuPrice}]
            },
            Stock:{
              rules: [{required: true,validator: this.checkStock}]
            },
          },
        }
      },
      methods:{
      //确定修改基本信息
        modifyBasic(){
          let that=this
          this.formTranslate.validateFields((err, values) => {
            that.form.spuimage=values.spuimage
            that.form.spuimage1=values.spuimage1
            that.form.spuimage2=values.spuimage2
            that.form.spuimage3=values.spuimage3
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
            'spuDetail': {
              'afterService': this.form.afterService,
              'description': this.form.description,
              'packingList': this.form.packingList,
            },
            'subTitle': this.form.subTitle,
            'title': this.form.title
          }
          httpAction('/kunze/spu/updateSpu', spuBo, 'post').then((res) => {
            if (res.success == true) {
              that.form.brand = []
              that.cids = []
              this.form.spuimage = ""
              that.form.afterService = ''
              that.form.description = ''
              that.form.packingList = ''
              that.form.subTitle = ''
              that.form.title = ''
              that.xiuBrandvisible = false
              that.visible = false
              that.$message.success('修改成功');
              this.getAllProducts(this.shopId)
            }
          })
        },
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
          console.log(e)
          this.sku=e
          let that=this

          this.xiuBrandvisible=true
          this.form.price=e.price
          this.form.stock=e.stock
          this.form.skuimage=e.skuimage
          this.$nextTick(() => {
            this.formTranslate.setFieldsValue(pick(this.form, 'title','brand', 'subTitle','price','stock','brand','skuimage','spuimage','spuimage1','spuimage2','spuimage3','afterService','packingList','description','newprice'))
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
            if(res.success==true){
              that.skudata=res.result
              that.skudata.forEach(e=>{
                e.skuimage=e.images
                e.images=window._CONFIG['domianURL']+'/'+e.images
                e.ownSpec=e.ownSpec.slice(1,e.ownSpec.length-1)
              })
              this.form.packingList= that.skudata[0].packingList
              this.form.afterService=that.skudata[0].afterService
              this.form.description=that.skudata[0].description
              that.spu=e
              that.spu.images=that.spu.images.split(",")
              this.form.title=this.spu.title
              this.form.subTitle=this.spu.subTitle
              this.brand.forEach(e=>{
                if(e.bname==this.spu.bname){
                  this.form.brand=e.bid
                }
              })
              this.form.spuimage= this.spu.spuimage
              this.form.spuimage1=this.spu.images[0]
              this.form.spuimage2=this.spu.images[1]
              this.form.spuimage3=this.spu.images[2]
              this.$nextTick(() => {
                this.formTranslate.setFieldsValue(pick(this.form, 'title','brand', 'subTitle','price','newprice','stock','brand','skuimage','spuimage','spuimage1','spuimage2','spuimage3','afterService','packingList','description'))
              });
              this.visible=true
            }

          })




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
            this.formTranslate.setFieldsValue(pick(this.form, 'title', 'subTitle','price','newprice','stock','skuimage','spuimage','spuimage1','spuimage2','spuimage3','afterService','packingList','description'))
          });
        },

        //点击修改sku取消按钮
        xiuhandleCancel(){
          this.xiuBrandvisible=false
        },
        //表单验证
        checkSpuPrice(rule, value, callback){
          // console.log(value)
          if(!value){
            callback("商品价格不能为空！");
          }else {
            var priceReg = /(^[1-9]\d*(\.\d{1,2})?$)|(^0(\.\d{1,2})?$)/;
            if(!value.match(priceReg)){
              callback("请输入正确的商品价格:整数或者保留两位小数");
            }else {
              callback();
            }
          }
        },
        checkStock(rule, value, callback){
          if(!value){
            callback("库存数量不能为空！");
          }else {
            var reg =/^([1-9][\d]{0,7}|0)?$/;
            if(!value.match(reg)){
              callback("库存数量只能输入数字！")
            }else {
              callback();
            }
          }
        },
        //点击修改sku确认按钮
        nextStep2(){
          console.log(32132)
          let that = this
          // 触发表单验证
          that.formTranslate.validateFields((err, values) => {
            console.log(values,11111)
            that.form.stock=values.stock
            that.form.price=values.price
            that.form.newprice=values.newprice
            that.form.skuimage=values.skuimage
            if(values.price,values.stock,values.skuimage,values.newprice){
              let priceReg = /(^[1-9]\d*(\.\d{1,2})?$)|(^0(\.\d{1,2})?$)/;
              if(!that.form.price.match(priceReg)){
                this.$message.warning('请输入正确的商品价格:整数或者保留两位小数');
              }else {
                if(!that.form.newprice.match(priceReg)){
                  this.$message.warning('请输入正确的商品优惠价格:整数或者保留两位小数');
                }else {



                    that.form.stock=values.stock
                    that.form.price=values.price
                    that.form.newprice=values.newprice
                    that.form.skuimage=values.skuimage
                    this.skuVos.push({
                      id: that.sku.id,
                      price: values.price,
                      stock: values.stock,
                      images:values.skuimage,
                      newPrice:values.newprice

                    })

                    let spuBo = {
                      'shopId': this.shopId,
                      "id": this.spu.id,
                      'skuVos': this.skuVos,
                    }
                    httpAction('/kunze/spu/updateSpu', spuBo, 'post').then((res) => {
                      console.log(res)
                      if (res.success == true) {
                        that.skuVos = []
                        that.xiuBrandvisible = false
                        that.visible = false
                        that.$message.success('修改成功');
                        this.getAllProducts(this.shopId)
                      }
                    })

                }
              }


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
        this.shopId=sessionStorage.getItem('shopId')
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
