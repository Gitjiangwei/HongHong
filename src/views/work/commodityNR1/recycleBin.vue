<template>
<!--    商品展示-->
  <div>
  <a-card title="商品展示" >

    <a-button type="primary" class="modifyBtn1" @click="deleteAllBrandBtn()">批量删除</a-button>
    <a-table :columns="columns" :data-source="data" :row-selection="{  onChange: onSelectChange }" >
         <span slot="image" slot-scope="text,record">
           <img :src=record.image alt="">
        </span>
      <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="xiuBrandBtn(record)">修改</a-button>
          <a-button type="primary" @click="deleteBrandBtn(record)">删除</a-button>
        </span>
    </a-table>
  </a-card>
  </div>
</template>

<script>
  import {getAction} from '../../../api/manage'



  export default {
    name: 'recycleBin',
      data(){
        return{
          columns:[
            { title: 'ID', dataIndex: 'id', key: 'id' },
            { title: '品牌名称', dataIndex: 'bname', key: 'bname' },
            { title: '图片', dataIndex: 'image', key: 'image',scopedSlots: { customRender: 'image' } },
            { title: '所属分类', dataIndex: 'cname', key: 'cname' },
            { title: '商品名称', dataIndex: 'title', key: 'title' },
            { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
          ],
          data:[],
        }
      },
      methods:{
        onSelectChange(selectedRowKeys, selectedRows) {
          let that=this
          selectedRows.forEach(e=>{
            that.bids .push(e.bid)
          })
        },
        //获取所有商品
        getAllProducts(){
          let that=this
          getAction('/kunze/spu/spuList',{pageSize:'1',shopId:'1'}).then((res)=>{
            getAction('/kunze/spu/spuList',{pageSize:res.result.pages,shopId:'1'}).then((res)=>{
              that.data=res.result.list
              let key=0
              that.data.forEach(e=>{
                e.image=window._CONFIG['domianURL']+'/'+e.image
                e.key=key++
              })
            })
          })
        }
      },
      mounted() {
      this.getAllProducts()
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
