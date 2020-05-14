<template>
<!--    新增商品-->
  <a-card title="新增商品">
    <a-form-model
      ref="ruleForm"
      :model="form"
      :rules="rules"
      :label-col="labelCol"
      :wrapper-col="wrapperCol"
    >
      <a-form-model-item label="品牌" prop="brand">
        <a-select v-model="form.brand" placeholder="选择品牌">
          <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys">
            {{v.keys}}
          </a-select-option>
        </a-select>
      </a-form-model-item>
      <a-form-model-item label="分类" prop="flei">
        <a-cascader
          :field-names="{ label: 'name', value: 'id', children: 'childrenList' }"
          :options="options"
          placeholder="Please select"
          @change="onChange"
        />
      </a-form-model-item>
      <a-form-model-item :wrapper-col="{ span: 14, offset: 4 }">
        <a-button type="primary" @click="onSubmit">
          Create
        </a-button>
        <a-button style="margin-left: 10px;" @click="resetForm">
          Reset
        </a-button>
      </a-form-model-item>
    </a-form-model>
  </a-card>

</template>

<script>
  import {getAction} from '../../../api/manage'

  export default {
    name: 'addcommodity',
      data(){
        return{
          labelCol: { span: 2 },
          wrapperCol: { span: 16 },
          other: '',
          form: {
            brand:'',
          },
          brand:[],  //品牌的集合
          options:[],  //分类级联下拉
          rules: {
            brand: [{ required: true, message: 'Please select Activity zone', trigger: 'change' }],
            flei: [{ required: true, message: 'Please select Activity zone', trigger: 'change' }],
          },
        }
      },
      methods:{
        onChange(value){
          console.log(value)
          getAction('/kunze/spec/specList',{categoryId:value[2]}).then((res)=>{
            // let nn=res.result.specifications


          })
        },
        onSubmit(){

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
      this.getBrand()
      }
  }
</script>

<style scoped>

</style>
