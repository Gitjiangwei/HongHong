<template>
<!--    管理分类-->
  <div>
    <a-card title="分类管理" >
      <a-table :columns="columns" :data-source="data"  >
        <a slot="sort" slot-scope="text" href="javascript:;">Delete</a>
        <a slot="category" slot-scope="text" href="javascript:;">Delete</a>
        <a slot="bianji" slot-scope="text" href="javascript:;">Delete</a>
      </a-table>
    </a-card>
  </div>
</template>

<script>
  import {getAction} from '../../../api/manage'

  export default {
        data() {
            return {
              columns:[
                { title: '分类名称', dataIndex: 'name', key: 'name' },
                { title: '排序', dataIndex: 'sort', key: 'sort', scopedSlots: { customRender: 'sort' } },
                { title: '添加子分类', dataIndex: 'id', key: 'id', scopedSlots: { customRender: 'category' } },
                { title: '编辑', dataIndex: 'isflag', key: 'isflag', scopedSlots: { customRender: 'bianji' } },
              ],
              data:[

              ]
            };
        },
        methods: {

          // 获取全部分类
          getAllCategories(){
            getAction('/kunze/category/qryList',{id:'',pid:''}).then((res)=>{
              this.data=res.result
              let key=0
              this.data.forEach(e=>{
                e.key=key++
                e.children=JSON.parse(JSON.stringify(e.childrenList))
                e.children.forEach(e=>{
                  e.key=key++
                  e.children=JSON.parse(JSON.stringify(e.childrenList))
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

</style>
