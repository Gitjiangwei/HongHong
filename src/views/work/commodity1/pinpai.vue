<template>
  <!--    商品品牌-->
  <div>
    <a-card title="品牌管理" >
      <a-button type="primary" class="modifyBtn1" @click="deleteAllCategories()">批量删除</a-button>
      <a-table :columns="columns" :data-source="data" :row-selection="{  onChange: onSelectChange }" >
        <span slot="bianji" slot-scope="text,record">
          <a-button type="primary" class="modifyBtn" @click="modifyCategories(record)">修改</a-button>
          <a-button type="primary" @click="deleteCategories(record)">删除</a-button>
        </span>
      </a-table>
    </a-card>


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
      };
    },
    methods: {

      // 获取全部分类
      getAllCategories(){

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
