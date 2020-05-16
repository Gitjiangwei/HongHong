<template>
<!--    新增商品-->
  <a-card title="新增商品">
    <div style="width: 65%">
    <a-form
      ref="ruleForm"
      :model="form"
      :label-col="labelCol"
      :wrapper-col="wrapperCol"
    >
      <a-form-item label="标题" >
        <a-input v-model="form.title" />
      </a-form-item>
      <a-form-item label="子标题" >
        <a-input v-model="form.subTitle" />
      </a-form-item>
      <a-form-item label="品牌" >
        <a-select v-model="form.brand" placeholder="选择品牌">
        <a-select-option  v-for="v in brand" :value=v.bid :key="v.keys">
          {{v.bname}}
        </a-select-option>
      </a-select>
      </a-form-item>
      <a-form-item label="分类" >
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
      <a-form-item label="商品图片" >
        <j-image-upload class="avatar-uploader" text="上传" v-model="fileList" ></j-image-upload>
      </a-form-item>
      <a-form-item label="销售价格" >
        <a-input v-model="form.price" placeholder="单位为分" />
      </a-form-item>
      <a-form-item label="商品库存" >
        <a-input v-model="form.stock" />
      </a-form-item>
      <a-form-item label="包装清单" >
        <a-input v-model="form.packingList" />
      </a-form-item>
      <a-form-item label="售后服务" >
        <a-input v-model="form.afterService" />
      </a-form-item>
      <a-form-item label="商品描述" >
        <j-editor v-model="form.description"/>
      </a-form-item>
      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
        <a-button type="primary" @click="onSubmit">
          添加
        </a-button>
        <a-button style="margin-left: 10px;" @click="resetForm">
          重置
        </a-button>
      </a-form-item>
    </a-form>
    </div>
  </a-card>

</template>

<script>
  import {getAction,httpAction} from '../../../api/manage'
  import JImageUpload from '../../../components/jeecg/JImageUpload'
  import JEditor from '@/components/jeecg/JEditor'

  import Vue from 'vue'
  export default {
    name: 'addcommodity',
    components: {
      JImageUpload,
      JEditor
    },
      data(){
        return{
          labelCol: { span: 4 },
          wrapperCol: { span: 14 },
          other: '',
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
          cids:[]
        }
      },
      methods:{

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
          // form: {
          //     price:"",
          //     stock:'',
          //     description:'',
          //     afterService:'',
          //     packingList:'',
          //     subTitle:'',
          //     title:'',
          // },

          let shopId=this.$store.state.shopId
          let spuBo={
            brandId: this.form.brand,
            cid1: this.cids[0],
            cid2: this.cids[1],
            cid3: this.cids[2],
            image: this.fileList,
            shopId:shopId,
            skuVos: [
              {
                indexes: this.indexes,
                ownSpec:  this.ownSpec,
                price: this.form.price,
                stock: this.form.stock
              }
            ],
            spuDetail: {
              afterService: this.form.afterService,
              description: this.form.description,
              packingList: this.form.packingList,
              specTemplate: this.specTemplate,
              specifications: this.specifications,
            },
            subTitle: this.form.subTitle,
            form:this.form.form
          }
          debugger;
          console.log(spuBo)
          // let param = new URLSearchParams()
          // param.append('spuBo',spuBo)
          httpAction('/kunze/spu/saveGood', spuBo,"post").then((res)=>{
            console.log(res)
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
</style>
