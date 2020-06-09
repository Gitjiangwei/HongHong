<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @cancel="handleCancel"
    @ok="handleSubmit"
    cancelText="关闭"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="选择商品"
          hasFeedback
        >
          <a-input @click="handleSpu" placeholder="请选择商品" maxlength="30" readonly
                   v-decorator="['spuName', {rules: [{ required: true, message: '请选择商品', }]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="商品规格"
          hasFeedback
        >
          <a-input @click="handleSku" placeholder="请选择商品规格" maxlength="30" readonly
                   v-decorator="['skuName', {rules: [{ required: true, message: '请选择商品规格', }]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="商品特卖价格"
          hasFeedback
        >
          <a-input placeholder="请输入商品特卖价格" maxlength="30"
                   v-decorator="['featuresPrice', {rules: [{ required: true, message: '请输入商品特卖价格', }]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="商品特卖库存"
          hasFeedback
        >
          <a-input placeholder="请输入商品特卖库存" maxlength="10"
                   v-decorator="['featuresStock', {rules: [{ required: true, message: '请输入商品特卖库存', }]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="顺序"
          hasFeedback
        >
          <a-input placeholder="请输入商品顺序" maxlength="10"
                   v-decorator="['featuresWeight', {rules: [{ required: true, message: '请输入商品顺序', }]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="特卖开始时间"
          hasFeedback
        >
          <a-date-picker
            v-decorator="['specialstartTime', {rules: [{ required: true, message: '请选择特卖开始时间', }]}]"
            show-time
            format="YYYY-MM-DD HH:mm:ss"
          />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="特卖结束时间"
          hasFeedback
        >
          <a-date-picker
            v-decorator="['specialendTime', {rules: [{ required: true, message: '请选择特卖结束时间', }]}]"
            show-time
            format="YYYY-MM-DD HH:mm:ss"
          />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注"
          hasFeedback
        >
          <a-textarea
            v-model="value"
            placeholder="请输入备注"
            :auto-size="{ minRows: 3, maxRows: 5 }"
            v-decorator="['remarks', {rules: [{}]}]"
          />
        </a-form-item>
      </a-form>
    </a-spin>
    <today-spu-list ref="TodaySpuList" @func="modalFormOkSpu"></today-spu-list>
    <today-sku-list ref="TodaySkuList" @func="modalFormOkSku"></today-sku-list>
  </a-modal>
</template>
<script>

  import ARow from "ant-design-vue/es/grid/Row";
  import {httpAction, getAction, postAction} from '@/api/manage';
  import {filterObj} from '@/utils/util';
  import TodaySpuList from "./TodaySpuList";
  import TodaySkuList from "./TodaySkuList";

  export default {
    name:"TodaySpecialModel",
    components: {
      ARow,
      TodaySpuList,
      TodaySkuList
    },
    data(){
      return{
        description: '今日特卖编辑页',
        title: "操作",
        visible: false,
        formData: {},
        model: {},
        shopId:"",
        labelCol: {
          xs: {span: 24},
          sm: {span: 5},
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16},
        },
        uploadLoading: false,
        headers: {},
        spuId:"",
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules: {},
        url: {
          add:"/kunze/features/saveSpuFeatures"
        },
      }
    },
    created() {
      const token = Vue.ls.get(ACCESS_TOKEN);
      this.headers = {"X-Access-Token": token};
      this.shopId=this.$store.state.shopId;
    },
    methods:{
      loadShopList(wheelId){
        queryByShopId({id:wheelId}).then((res)=>{
          if(res.success){
            this.selectedRole = res.result;
          }else{
            console.log(res.message);
          }
        });
      },
      add() {
        this.edit({});
      },
      edit(record) {
        let that = this;
        that.initialShopList();
        that.visible = true;
        that.form.resetFields();
        that.model = Object.assign({}, record);
        that.$nextTick(() => {
          that.form.setFieldsValue(pick(this.model, 'spuId', 'skuId','featuresPrice','featuresStock','featuresWeight','specialstartTime','specialendTime','remarks'))
         //时间格式化
          this.form.setFieldsValue({specialstartTime: this.model.specialstartTime ? moment(this.model.specialstartTime, 'YYYY-MM-DD HH:mm:ss') : null});
          this.form.setFieldsValue({specialendTime: this.model.specialendTime ? moment(this.model.specialendTime, 'YYYY-MM-DD HH:mm:ss') : null});
        });

      },
      handleSpu(){
        this.$refs.TodaySpuList.spuList();
        this.$refs.TodaySpuList.title="选择商品";
      },
      handleSku(){
        if(this.spuId==null||this.spuId==""||this.spuId==undefined){
          this.$message.warning("请先选择商品！");
          return;
        }
        this.$refs.TodaySkuList.skuList(this.spuId);
        this.$refs.TodaySpuList.title="选择商品规格";
      },
      close() {
        this.$emit('close');
        this.visible = false;
        this.fileList=[];
      },
      initialShopList(){
        getAction(this.url.shopList).then((res)=>{
          if(res.success){
            this.shopList = res.result;
          }else{
            console.log(res.message);
          }
        });
      },
      modalFormOkSpu(data) {
        this.spuId = data.id;
        this.model.spuId = data.id;
        this.form.setFieldsValue({spuName:data.title});
      },
      modalFormOkSku(data) {
        this.model.skuId = data.id;
        this.form.setFieldsValue({skuName:data.ownSpec});
      },
      handleSubmit() {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            debugger;
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if (!this.model.featuresId) {
              httpurl += this.url.add;
              method = 'post';
            } else {
              httpurl += this.url.edit;
              method = 'post';
            }
            let formData = Object.assign(this.model, values);
            formData.shopId = this.$store.state.shopId;
            formData.featuresFlag = "1";
            //时间格式化
            formData.specialstartTime = formData.specialstartTime ? formData.specialstartTime.format('YYYY-MM-DD HH:mm:ss') : null;
            formData.specialendTime = formData.specialendTime ? formData.specialendTime.format('YYYY-MM-DD HH:mm:ss') : null;
            httpAction(httpurl, formData, method).then((res) => {
              if (res.success) {
                that.$message.success(res.message);
                that.$emit('ok');
              } else {
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })


          }
        })
      },
      // 根据屏幕变化,设置抽屉尺寸
      resetScreenSize(){
        let screenWidth = document.body.clientWidth;
        if(screenWidth < 500){
          this.drawerWidth = screenWidth;
        }else{
          this.drawerWidth = 700;
        }
      },
      handleCancel() {
        this.close()
      },
    },
  }
</script>