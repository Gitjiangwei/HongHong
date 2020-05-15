<template>
  <!--    商品参数规格管理-->
  <div>
    <a-card title="商品参数规格管理">
      <a-tree
        v-model="checkedKeys"
        :auto-expand-parent="autoExpandParent"
        :selected-keys="selectedKeys"
        :tree-data="treeData"
        :replace-fields="replaceFields"
        @select="onSelect"
      />
    </a-card>


<!--    弹出参数模板-->
    <a-modal
      title="商品参数规格管理"
      :visible="visible"
      :confirm-loading="confirmLoading"
      :width="width"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <template v-for=" v in csdatas">
        <div class="nnn">

          <h4 class="nnn" style="float: left">{{v.group}}</h4>
          <div>
            <a-button class="modifyBtn" @click="addxinsx(v.group)" >添加新属性</a-button>
            <a-button  icon="delete"  style="margin-bottom: 5px" @click="deleteFenzu(v.group)"  ></a-button>
          </div>
        </div>
        <a-table :columns="columns" :data-source="v.params" size="small" :pagination="false" >
          <span slot="bianji" slot-scope="text, record">
            <a @click="xiugaishuxin(record)">修改</a>
            <a-divider type="vertical" />
            <a @click="shanchushuxin(record)">删除</a>
          </span>
        </a-table>
      </template>
      <a-button  type="primary" @click="addxiFenzhu" style="margin-left: 80%" >添加新属性</a-button>
    </a-modal>

<!--    添加新的分组弹出框-->
    <a-modal
      title="添加新的分组"
      :visible="fenzuvisible"
      :confirm-loading="fenzuconfirmLoading"
      @ok="fenzuhandleOk"
      @cancel="fenzuhandleCancel"
    >
      <a-form-model
        ref="ruleForm"
        :model="fenzudatas"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
      >
        <a-form-model-item label="分组名" prop="brand">
          <a-input v-model="fenzudatas.group"/>
        </a-form-model-item>
      </a-form-model>

    </a-modal>

<!--    添加新属性弹出框-->
    <a-modal
      title="添加新的属性"
      :visible="shuxinvisible"
      :confirm-loading="shuxinconfirmLoading"
      @ok="shuxinhandleOk"
      @cancel="shuxinhandleCancel"
    >
      <a-form-model
        ref="ruleForm"
        :model="shuxindatas"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
      >
        <a-form-model-item label="属性名" prop="brand">
          <a-input v-model="shuxindatas.k"/>
        </a-form-model-item>
        <a-form-model-item label="属性值" prop="brand">
          <a-input v-model="shuxindatas.options"/>
        </a-form-model-item>
      </a-form-model>

    </a-modal>

<!--    修改属性弹出框-->
    <a-modal
      title="修改属性"
      :visible="xiugaivisible"
      :confirm-loading="xiugaiconfirmLoading"
      @ok="xiugaihandleOk"
      @cancel="xiugaihandleCancel"
    >
      <a-form-model
        ref="ruleForm"
        :model="xiugaidatas"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
      >
        <a-form-model-item label="属性名" prop="brand">
          <a-input v-model="xiugaidatas.k"/>
        </a-form-model-item>
        <a-form-model-item label="属性值" prop="brand">
          <a-input v-model="xiugaidatas.options"/>
        </a-form-model-item>
      </a-form-model>

    </a-modal>

    <!--    修删除属性弹出框-->
    <a-modal
      title="删除属性"
      :visible="shanchuvisible"
      :confirm-loading="shanchuconfirmLoading"
      @ok="shanchuhandleOk"
      @cancel="shanchuhandleCancel"
    >
      <p>是否要删除该属性</p>
    </a-modal>

    <!--    添加属性弹出框-->
    <a-modal
      title="添加属性"
      :visible="tianjiavisible"
      :confirm-loading="tianjiaconfirmLoading"
      @ok="tianjiahandleOk"
      @cancel="tianjiahandleCancel"
    >
      <a-form-model
        ref="ruleForm"
        :model="tianjiadatas"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
      >
        <a-form-model-item label="分组名" prop="brand">
          <a-input v-model="tianjiadatas.group"/>
        </a-form-model-item>
        <a-form-model-item label="属性名" prop="brand">
          <a-input v-model="tianjiadatas.params[0].k"/>
        </a-form-model-item>
        <a-form-model-item label="属性值" prop="brand">
          <a-input v-model="tianjiadatas.params[0].options"/>
        </a-form-model-item>
      </a-form-model>

    </a-modal>

  </div>
</template>

<script>
  import {getAction,postAction,deleteAction} from '../../../api/manage'
  const columns = [
    {
      title: '参数名',
      dataIndex: 'k',
      key: 'key',
    },
    {
      title: '参数',
      dataIndex: 'options',
      key: 'key1',
    },
    { title: '编辑', dataIndex: 'key', key: 'key3', scopedSlots: { customRender: 'bianji' },fixed: 'right' },
  ];

  export default {
    data(){
      return {
        columns,
        width:'60%',
        visible: false,
        fenzuvisible: false,
        tianjiavisible: false,
        shuxinvisible: false,
        xiugaivisible: false,
        shanchuvisible: false,
        confirmLoading: false,
        tianjiaconfirmLoading: false,
        fenzuconfirmLoading: false,
        shanchuconfirmLoading: false,
        shuxinconfirmLoading: false,
        xiugaiconfirmLoading: false,
        replaceFields:{
          children:'childrenList',
          title:'name',
          key:'id'
        },
        autoExpandParent: true,
        checkedKeys: [],
        selectedKeys: [],
        treeData:[],
        csdatas:[],  //所有参数
        fenzudatas:{
          group:'',
          params:[]
        },
        tianjiadatas:{
          group:'',
          params:[
            {
              global: true,
              k: '',
              options: ""
            }
          ]
        },
        shuxindatas:{
          global: true,
          k: '',
          options: ""
        },
        xiugaidatas:{
          global: true,
          k: '',
          options: ""
        },
        labelCol: { span: 4 },
        wrapperCol: { span: 14 },
        grop:'', //添加新属性group记录
        k:'', //修改属性k记录
    }
    },

    methods: {
      tianjiahandleOk(){
        let that=this
        if(that.tianjiadatas.group==''){
          that.$message.warning('分组名称不能为空');
        }else if (that.tianjiadatas.params[0].k==''){
          that.$message.warning('属性名称不能为空');
        }else if(that.tianjiadatas.params[0].options==''){
          that.$message.warning('属性值不能为空');
        }else {
          that.csdatas=[]
         JSON.parse(JSON.stringify(that.csdatas.push(that.tianjiadatas)))
          let param = new URLSearchParams()
          param.append('categoryId',that.selectedKeys[0])
          param.append('specifications' ,JSON.stringify(that.csdatas) )
          postAction('/kunze/spec/saveSpec',param).then((res)=>{

            if(res.success==true){
              that.hqcsmb()
              that.$message.success('添加分组成功');
              that.tianjiavisible=false
              that.tianjiadatas.group=''
              that.tianjiadatas.params[0].k=''
              that.tianjiadatas.params[0].options=''
            }else {
              that.$message.warning('添加分组失败');
              that.tianjiavisible=false
              that.tianjiadatas.group=''
              that.tianjiadatas.params[0].k=''
              that.tianjiadatas.params[0].options=''
            }
          })
        }



      },
      tianjiahandleCancel(){
        this.tianjiavisible=false
        this.tianjiadatas.group=''
        this.tianjiadatas.params[0].k=''
        this.tianjiadatas.params[0].options=''
      },
      shanchuhandleOk(){
        let that=this
        this.csdatas.forEach(e=>{
          e.params.forEach((y,i)=>{
            if(y.k==that.k){
              e.params.splice(i,1)
            }
          })
        })
        let params={
          categoryId: that.selectedKeys[0],
          specifications:that.csdatas
        }
        postAction('/kunze/spec/updateSpec',params).then((res)=>{
          if(res.success==true){
            that.hqcsmb()
            that.$message.success('删除属性成功');
            that.shanchuvisible=false
          }else {
            that.$message.warning('删除属性失败');
            that.shanchuvisible=false
          }
        })

      },
      shanchuhandleCancel(){
        this.shanchuvisible=false
      },
      //删除属性
      shanchushuxin(e){
        this.shanchuvisible=true
        this.k=e.k
      },
      xiugaihandleOk(){
        let that=this
        if(that.xiugaidatas.k==''){
          that.$message.warning('属性名不能为空');
        }else if(that.xiugaidatas.options==''){
          that.$message.warning('属性值不能为空');
        }else {
          this.csdatas.forEach(e=>{
            e.params.forEach((e,i)=>{
              if(e.k==that.k){
                e.k=that.xiugaidatas.k
                e.options=that.xiugaidatas.options
              }
            })
          })

          let params={
            categoryId: that.selectedKeys[0],
            specifications:that.csdatas
          }
          postAction('/kunze/spec/updateSpec',params).then((res)=>{
            if(res.success==true){
              that.hqcsmb()
              that.$message.success('修改属性成功');
              that.xiugaivisible=false
              that.xiugaidatas.k=''
              that.xiugaidatas.options=''
            }else {
              that.$message.warning('修改属性失败');
              that.xiugaivisible=false
              that.xiugaidatas.k=''
              that.xiugaidatas.options=''
            }
          })
        }

      },
      xiugaihandleCancel(){
        this.xiugaivisible=false
        this.xiugaidatas.k=''
        this.xiugaidatas.options=''
      },
      //点击修稿按钮
      xiugaishuxin(w){
        let that=this
        this.k=w.k
        this.xiugaivisible=true
        this.csdatas.forEach(e=>{
          e.params.forEach((e,i)=>{
            if(e.k==that.k){
              that.xiugaidatas.k=e.k
              that.xiugaidatas.options=e.options
            }
          })
        })

      },
      shuxinhandleOk(){
        let that=this
        if( this.shuxindatas.k==''){
          that.$message.warning('属性名称不能为空')
        }else if(this.shuxindatas.options==''){
          that.$message.warning('属性值不能为空')
        }else {
          this.csdatas.forEach(e=>{
            if(e.group==that.grop){
              e.params[e.params.length]=JSON.parse(JSON.stringify(that.shuxindatas))
            }
          })
          let params={
            categoryId: that.selectedKeys[0],
            specifications:that.csdatas
          }
          postAction('/kunze/spec/updateSpec',params).then((res)=>{

            if(res.success==true){
              this.hqcsmb()
              this.$message.success('添加属性成功');
              this.shuxinvisible=false
              this.shuxindatas.k=''
              this.shuxindatas.options=''
            }else {
              this.$message.warning('添加属性失败');
              this.shuxinvisible=false
              this.shuxindatas.k=''
              this.shuxindatas.options=''
            }
          })
        }



      },
      shuxinhandleCancel(){
        this.shuxinvisible=false
        this.shuxindatas.k=''
        this.shuxindatas.options=''
      },
      //添加新的属性
      addxinsx(w){
        this.shuxinvisible=true
        this.grop=w
      },
      // 添加分组取消按钮
      fenzuhandleCancel(){
        this.fenzuvisible=false
        this.fenzudatas.group=''
      },
      // 添加新分组确认按钮
      fenzuhandleOk(){
        let that=this
        if(that.fenzudatas.group==''){
          that.$message.warning('分组名称不能为空');
        }else {




            that.csdatas[that.csdatas.length]=JSON.parse(JSON.stringify(that.fenzudatas))
            let params={
              categoryId: that.selectedKeys[0],
              specifications:that.csdatas

            }


            postAction('/kunze/spec/updateSpec',params).then((res)=>{

              if(res.success==true){
                that.hqcsmb()
                that.$message.success('添加分组成功');
                that.fenzuvisible=false
                that.fenzudatas.group=''
              }else {
                that.$message.warning('添加分组失败');
                that.fenzuvisible=false
                that.fenzudatas.group=''
              }
            })


        }


      },
      //添加新的分组
      addxiFenzhu(){
        this.fenzuvisible=true
      },
      //删除分组
      deleteFenzu(v){
        let that=this
        this.$confirm({
          title: '删除',
          content: '确定要删除该分组吗 ?',
          onOk() {
            that.csdatas.forEach((e,i)=>{
              if(e.group==v){
                that.csdatas.splice(i,1)
              }
            })

            let params={
              categoryId: that.selectedKeys[0],
              specifications:that.csdatas
            }
            if(that.csdatas.length==0){

              deleteAction('/kunze/spec/delSpec',{categoryId:that.selectedKeys[0]}).then((res)=>{
                // console.log(res)
                if(res.success==true){
                      that.hqcsmb()
                      that.$message.success('删除成功');
                      that.visible=false
                      that.tianjiavisible=true
                    }else {
                      that.$message.warning('删除失败');
                    }
              })
            }else {
              postAction('/kunze/spec/updateSpec',params).then((res)=>{
                if(res.success==true){
                  that.hqcsmb()
                  that.$message.success('删除成功');
                }else {
                  that.$message.warning('删除失败');
                }
              })
            }


          },
          onCancel() {
          },
        })


      },
      onSelect(selectedKeys, info) {
        let that=this
        this.selectedKeys = selectedKeys;
        // console.log( selectedKeys[0]);
        this.treeData.forEach(e=>{
          if(e.id==selectedKeys[0]){

          }else {
            e.childrenList.forEach(e=>{
              if(e.id==selectedKeys[0]){

              }else {

                e.childrenList.forEach(e=>{
                  if(e.id==selectedKeys[0]){
                    getAction('/kunze/spec/specList',{categoryId:selectedKeys[0]}).then((res)=>{
                      // console.log(res.result)
                      if(res.result==null){
                        // that.csdatas=JSON.parse(res.result.specifications)
                        that.tianjiavisible=true
                      }else {
                        that.csdatas=JSON.parse(res.result.specifications)
                        // console.log(that.csdatas)
                        let key=0
                        that.csdatas.forEach(e=>{
                          e.params.forEach(e=>{
                            e.key=key++
                            e.key1=key++
                            e.key3=key++
                            // if(e.options=='"\\"\\""'){
                            //   e.options=''
                            // }else {
                            //   e.options=JSON.parse(JSON.parse(e.options))
                            // }
                          })
                        })
                        // console.log(that.csdatas.length)
                        if(that.csdatas.length==0){
                          that.tianjiavisible=true
                        }else {
                          that.visible = true;
                        }
                      }

                    })




                  }
                })
              }
            })
          }
        })
      },
      hqcsmb(){
        let that=this
        getAction('/kunze/spec/specList',{categoryId:this.selectedKeys[0]}).then((res)=>{
          if(res.result==null){

          }else {

            that.csdatas=JSON.parse(res.result.specifications)
            let key=0
            that.csdatas.forEach(e=>{
              e.params.forEach(e=>{
                e.key=key++
                e.key1=key++
                e.key3=key++
                // if(e.options=='"\\"\\""'){
                //   e.options=''
                // }else {
                //   e.options=JSON.parse(JSON.parse(e.options))
                // }
              })
            })
          }
        })
      },
      hqfle(){
        getAction('/kunze/category/qryList',{id:'',pid:''}).then((res)=>{
          this.treeData=res.result
        })
      },
      handleOk(e) {
        this.visible = false;
        this.csdatas=[]
      },
      handleCancel(e) {
        this.visible = false;
        this.csdatas=[]
      },

    },
    mounted () {
      this.hqfle()
    }

  }
</script>

<style scoped>
  /deep/.ant-spin-nested-loading{
    margin-bottom: 10px;
  }
  .modifyBtn{
    margin-right: 10px;
  }
 .nnn{
   display: flex;
   justify-content: space-between;
 }

</style>
