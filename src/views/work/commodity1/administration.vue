<template>
<!--    管理分类-->
  <div>
    <a-card title="分类管理" >
      <a-button type="primary" class="modifyBtn1" @click="deleteAllCategories()">批量删除</a-button>
      <a-table :columns="columns" :data-source="data" :row-selection="{  onChange: onSelectChange }" >
        <span slot="sort" slot-scope="text,record" >
            <a @click="addCategories(record)">排序</a>
        </span>
        <span slot="category" slot-scope="text,record">
          <a @click="addCategories(record)">添加子分类</a>
        </span>
        <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="modifyCategories(record)">修改</a-button>
          <a-button type="primary" @click="deleteCategories(record)">删除</a-button>
        </span>
      </a-table>
    </a-card>

<!--    添加子分类弹出框-->
    <a-modal
      title="添加子分类"
      :visible="addvisible"
      :confirm-loading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <a-form-model :model="form" :label-col="{ span: 6 }" :wrapper-col="{ span: 12 }">
        <a-form-model-item label="分类名">
          <a-input v-model="form.name" />
        </a-form-model-item>
        <a-form-model-item label="是否是父级分类">
          <a-input v-model="form.isParent"  placeholder="1代表是，0表示否" />
        </a-form-model-item>
        <a-form-model-item label="排序">
          <a-input v-model="form.sort"  />
        </a-form-model-item>
      </a-form-model>
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

  </div>
</template>

<script>
  import {getAction} from '../../../api/manage'
  import {postAction} from '../../../api/manage'
  import {deleteAction} from '../../../api/manage'


  export default {
        data() {
            return {
              columns:[
                { title: '分类名称', dataIndex: 'name', key: 'name' },
                { title: '排序', dataIndex: 'sort', key: 'sort', scopedSlots: { customRender: 'sort' } },
                { title: '添加子分类', dataIndex: 'id', key: 'id', scopedSlots: { customRender: 'category' } },
                { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
              ],
              data:[],
              addvisible: false, //控制添加弹出框
              modifyvisible: false, //控制添加弹出框
              confirmLoading: false,
              form:{
                name:'',
                isParent:'',
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
          onSelectChange(selectedRowKeys, selectedRows) {
            // console.log(selectedRows)
            let that=this
            selectedRows.forEach(e=>{
              that.ids.push(e.id)
            })
            console.log(1,selectedRows)
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
                  console.log(res)
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
            this.$confirm({
              title: '确定要删除该分类吗?',
              content: '该分类包含多个子分类',
              okText: '确定',
              okType: 'primary',
              cancelText: '取消',
              onOk() {
                deleteAction('/kunze/category/categoryDel',{id:id}).then((res)=>{
                  if(res.success==true){
                    this.$message.success(
                      '删除成功'
                    );
                    this.getAllCategories()
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
          },
          // 点击修改按钮
          modifyCategories(e){
            this.pid=e.id
            this.modifyform.name=e.name
            this.modifyvisible=true
          },
          // 点击添加子分类弹窗确认按钮
          handleOk(){
            this.addvisible=false
            let category={
              isParent: this.form.isParent,
              name:this.form.name,
              parentId:this.pid,
              image: "string",
              index: "string",
              isflag: "string",
              sort:this.form.sort
            }
            postAction('/kunze/category/categorySave',category).then((res)=>{
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
              this.form.isParent=''
            })

          },
          // 点击添加子分类弹窗取消按钮
          handleCancel(){
            this.addvisible=false
            this.form.name=''
            this.form.sort=''
            this.form.isParent=''
          },
          // 点击添加子集分类
          addCategories(e){
            // console.log(e.isParent)
            this.pid=e.id
            if(e.isParent==0){
              this.$message.warning('底层分类不可添加子分类');
            }
            if(e.isParent==1){
              this.addvisible=true
            }
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
  }
</style>
