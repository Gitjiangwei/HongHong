<template>
<!--    管理分类-->
  <div>
    <a-card title="分类管理" >
      <a-button type="primary" class="modifyBtn1" @click="deleteAllCategories()">批量删除</a-button>
      <a-button type="primary" class="modifyBtn1" @click="adddjfl()">添加顶级分类</a-button>
      <a-table :columns="columns" :data-source="data" :row-selection="{  onChange: onSelectChange }" :pagination="false" >
<!--        <span slot="sort" slot-scope="text,record" >-->
<!--            <a @click="addCategories(record)">排序</a>-->
<!--        </span>-->
        <span slot="category" slot-scope="text,record">
          <a @click="addCategories(record)" v-if="record.isParent==1">添加子分类</a>
          <a @click="addCategoriesImg(record)" v-if="record.isParent==0">添加图片</a>
        </span>
        <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="modifyCategories(record)">修改</a-button>
          <a-button type="primary" @click="deleteCategories(record)">删除</a-button>
        </span>
      </a-table>
    </a-card>


    <!--    添加顶级分类弹出框-->
    <a-modal
      title="添加顶级分类"
      :visible="adddjvisible"
      :confirm-loading="confirmLoading"
      @ok="adddjhandleOk"
      @cancel="adddjhandleCancel"
    >
      <a-form-model :model="adddjform" :label-col="{ span: 6 }" :wrapper-col="{ span: 12 }">
        <a-form-model-item label="分类名">
          <a-input v-model="adddjform.name" />
        </a-form-model-item>
        <a-form-model-item label="排序">
          <a-input v-model="adddjform.sort"  />
        </a-form-model-item>
      </a-form-model>
    </a-modal>

<!--    添加子分类弹出框-->
    <a-modal
      title="添加子分类"
      :visible="addvisible"
      :confirm-loading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <a-form :form="formTranslate" :label-col="{ span: 6 }" :wrapper-col="{ span: 12 }">
        <a-form-item label="分类名" hasFeedback>
          <a-input  v-decorator="['name', {rules: [{ required: true, message: '请输入分类名', }]}]" />
        </a-form-item>
<!--        <a-form-item label="是否是父级分类" hasFeedback>-->
<!--          <a-input v-model="form.isParent"  placeholder="1代表是，0表示否" />-->
<!--        </a-form-item>-->
        <a-form-item label="排序" hasFeedback>
          <a-input  v-decorator="['sort', {rules: [{ required: true, message: '请输入排序', }]}]" />
        </a-form-item>
      </a-form>
    </a-modal>

    <!--    修改分类弹出框-->
    <a-modal
      title="修改分类"
      :visible="modifyvisible"
      :confirm-loading="confirmLoading"
      @ok="modifyhandleOk"
      @cancel="modifyhandleCancel"
    >
      <a-form-model :model="form" :label-col="{ span: 6 }" :wrapper-col="{ span: 12 }">
        <a-form-model-item label="分类名">
          <a-input v-model="modifyform.name" />
        </a-form-model-item>
      </a-form-model>
    </a-modal>

    <!--    三级分类添加图片弹出窗-->
    <a-modal
      title="添加图片"
      :visible="addvisibleImg"
      :confirm-loading="confirmLoading"
      @ok="addImgOk"
      @cancel="addImgCancel"
    >
      <div style="display: flex;justify-content: left;">
      <img :src=fileList1ss alt=""  style="margin-right: 10px">
      <j-image-upload  class="avatar-uploader" text="上传" v-model="fileList1" style="width: 104px;margin-right: 30px" ></j-image-upload>
      </div>
    </a-modal>

  </div>
</template>

<script>
  import {getAction,postAction,deleteAction} from '../../../api/manage'
  import JImageUpload from '../../../components/jeecg/JImageUpload'
  import pick from 'lodash.pick'


  export default {
    components: {
      JImageUpload
    },
        data() {
            return {
              columns:[
                { title: '分类名称', dataIndex: 'name', key: 'name' },
                // { title: '排序', dataIndex: 'sort', key: 'sort', scopedSlots: { customRender: 'sort' } },
                { title: '添加子分类', dataIndex: 'id', key: 'id', scopedSlots: { customRender: 'category' } },
                { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
              ],
              data:[],
              addvisible: false, //控制添加弹出框
              addvisibleImg:false, //控制添加图片弹出框
              adddjvisible: false, //控制添加弹出框
              modifyvisible: false, //控制添加弹出框
              confirmLoading: false,
              isdataimg:false,
              fileList1:"",
              fileList1ss:"",
              formTranslate: this.$form.createForm(this),
              citem3:{},
              form:{
                name:'',
                isParent:'',
                sort:''
              },
              adddjform:{
                name:'',
                sort:''
              },
              pid:'',  //点击添加子分类获取到的父级
              modifyform:{
                name:''
              },
              ids:[]
            };
        },
        methods: {


          edit(record) {
            this.$nextTick(() => {
              this.formTranslate.setFieldsValue(pick(this.form, 'name', 'sort','price','stock'))
            });

          },

          onSelectChange(selectedRowKeys, selectedRows) {
            // console.log(selectedRows)
            let that=this
            selectedRows.forEach(e=>{
              that.ids.push(e.id)
            })
            // console.log(1,selectedRows)
          },
          // 点击批量删除按钮
          deleteAllCategories(){
            let that=this
            this.$confirm({
              title: '确定要删除该分类吗?',
              content: '该分类包含多个子分类',
              okText: '确定',
              okType: 'primary',
              cancelText: '取消',
              onOk() {
                let param = new URLSearchParams()
                param.append('ids',that.ids)
                deleteAction('/kunze/category/categoryDels',param).then((res)=>{

                  if(res.success==true){
                    that.$message.success(
                      '删除成功'
                    );
                    that.getAllCategories()
                  }
                })
              },
              onCancel() {

              },
            });
          },
          //点击删除按钮
          deleteCategories(e){
            let id=e.id
            let that=this
            this.$confirm({
              title: '确定要删除该分类吗?',
              content: '该分类包含多个子分类',
              okText: '确定',
              okType: 'primary',
              cancelText: '取消',
              onOk() {
                deleteAction('/kunze/category/categoryDel',{id:id}).then((res)=>{
                  if(res.success==true){
                    that.$message.success(
                      '删除成功'
                    );
                    that.getAllCategories()
                  }
                })
              },
              onCancel() {

              },
            });
          },
          // 点击修改取消按钮
          modifyhandleCancel(){
            this.modifyvisible=false
            this.modifyform.name=''
          },
          //点击修改确认按钮
          modifyhandleOk(){

            if(this.modifyform.name==''){
              this.$message.error('分类名不能为空');
            }else {
              this.modifyvisible=false
              let category={
                id: this.pid,
                name: this.modifyform.name
              }
              postAction('/kunze/category/categoryUpdate',category).then((res)=>{
                if(res.success){
                  this.$message.success(
                    '修改成功'
                  );
                  this.getAllCategories()
                }else {
                  this.$message.error('修改失败');
                  this.modifyform.name=''
                }
                this.modifyform.name=''
              })
            }

          },
          // 点击修改按钮
          modifyCategories(e){
            console.log(e)
            this.pid=e.id
            this.modifyform.name=e.name
            this.modifyvisible=true
          },
          adddjhandleOk(){
            if(this.adddjform.name==''){
              this.$message.error('分类名不能为空');
            }else if(this.adddjform.sort==''){
              this.$message.error('排序不能为空');
            }else {
              this.adddjvisible=false
              let category={
                isParent:'1',
                name:this.adddjform.name,
                parentId:'0',
                image: "string",
                index: "string",
                isflag: "string",
                sort:this.adddjform.sort
              }
              postAction('/kunze/category/categorySave',category).then((res)=>{
                console.log(res)
                if(res.success){
                  this.$message.success(
                    '添加成功'
                  );
                  this.getAllCategories()
                }else {
                  this.$message.error('添加失败');
                }
                this.adddjform.name=''
                this.adddjform.sort=''
              })
            }
          },
          adddjhandleCancel(){
            this.adddjvisible=false
            this.adddjform.name=''
            this.adddjform.sort=''
          },
          //点击添加顶级分类按钮
          adddjfl(){
            this.adddjvisible=true
          },
          // 点击添加子分类弹窗确认按钮
          handleOk(){
            let that = this
            // 触发表单验证
            this.formTranslate.validateFields((err, values) => {
              if(values.name && values.sort){
                that.form.name=values.name
                that.form.sort=values.sort



                this.addvisible=false
                    let category={
                      isParent: 1,
                      name:that.form.name,
                      parentId:this.pid,
                      image: "string",
                      index: "string",
                      isflag: "string",
                      sort:that.form.sort
                    }
                    postAction('/kunze/category/categorySave',category).then((res)=>{
                      console.log(res)
                      if(res.success){
                        this.$message.success(
                          '添加成功'
                        );
                        this.getAllCategories()
                      }else {
                        this.$message.error('添加失败');
                      }
                      this.form.name=''
                      this.form.sort=''
                    })

              }
            })
          //
          //   if(this.form.name==''){
          //     this.$message.error('分类名不能为空');
          //   }else if(this.form.sort==''){
          //     this.$message.error('排序不能为空');
          //   }else if(this.form.isParent==''){
          //     this.$message.error('请确定是否为父分类');
          //   }else {
          //     this.addvisible=false
          //     let category={
          //       isParent: this.form.isParent,
          //       name:this.form.name,
          //       parentId:this.pid,
          //       image: "string",
          //       index: "string",
          //       isflag: "string",
          //       sort:this.form.sort
          //     }
          //     postAction('/kunze/category/categorySave',category).then((res)=>{
          //       if(res.success){
          //         this.$message.success(
          //           '添加成功'
          //         );
          //         this.getAllCategories()
          //       }else {
          //         this.$message.error('添加失败');
          //       }
          //       this.form.name=''
          //       this.form.sort=''
          //       this.form.isParent=''
          //     })
          //   }


          },

          // 点击添加子分类弹窗取消按钮
          handleCancel(){
            this.addvisible=false
            this.form.name=''
            this.form.sort=''
            this.form.isParent=''
          },
          addImgOk(){

            if(this.fileList1==""){
              this.addvisibleImg=false
            }else {
              let category=this.citem3
              category.image=this.fileList1
              postAction('kunze/category/categoryUpdate',category).then((res)=>{
                // console.log(res)
                if(res.success==true){
                  this.$message.success('添加成功')
                  this.fileList1=""
                  this.addvisibleImg=false
                }
              })
            }


          },
          addImgCancel(){

            this.addvisibleImg=false
          },
          //点击三级分类添加图片
          addCategoriesImg(e){
            if(e.image==null){
              this.fileList1=""
              this.fileList1ss=""
            }else {
              this.fileList1ss= window._CONFIG['staticDomainURL'] +"/"+e.image
            }
            this.citem3=e
            this.addvisibleImg=true
          },
          // 点击添加子集分类
          addCategories(e){
            this.addvisible=true

          },
          // 获取全部分类
          getAllCategories(){
            getAction('/kunze/category/qryList',{id:'',pid:''}).then((res)=>{
              this.data=res.result
              let key=0
              this.data.forEach(e=>{
                e.key=key++
                e.children=JSON.parse(JSON.stringify(e.childrenList))
                e.childrenList=''
                e.children.forEach(e=>{
                  e.key=key++
                  e.children=JSON.parse(JSON.stringify(e.childrenList))
                  e.childrenList=''
                  e.children.forEach(e=>{
                    e.key=key++
                  })
                })
              })
              // console.log(this.data)
            })
          }
        },
        mounted(){
          this.getAllCategories()
        }
    };
</script>

<style scoped>
.modifyBtn{
  margin-right: 10px;
}
  .modifyBtn1{
    margin-bottom: 10px;
    margin-right: 10px;
  }
</style>
