<template>
  <a-modal
    :title="title"
    :width="1600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @cancel="handleCancel"
    @ok="handleOk"
    cancelText="关闭"
  >
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :span="8">
            <a-form-item label="超市名称" >
              <a-input placeholder="请输入超市名称"  v-model="queryParam.shopName"></a-input>
            </a-form-item>
          </a-col>
<!--          <a-col :span="8">
            <a-form-item label="商品分类">
              <a-cascader
                :field-names="{ label: 'name', value: 'id', children: 'childrenList'}"
                :options="options"
                placeholder="选择所属分类"
                :value="cids"
                @change="onChangeShop"
              />
            </a-form-item>
          </a-col>-->
          <a-col :span="8"  >
              <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
                <!--<a-button type="primary" @click="superQuery" icon="filter" style="margin-left: 8px">高级查询</a-button>-->
              </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- table区域-begin -->
    <div>
      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange,type:'radio'}"
        @change="handleTableChange">

        <template slot="avatarslot" slot-scope="text, record, index">
          <div class="anty-img-wrap">
            <a-avatar shape="square" :src="getAvatarView(record.image)" icon="user"/>
          </div>
        </template>
      </a-table>
    </div>
  </a-modal>
</template>
<script>

  import ARow from "ant-design-vue/es/grid/Row";
  import {deleteAction, getAction, postAction,getFileAccessHttpUrl} from '@/api/manage';
  import {filterObj} from '@/utils/util';

  export default {
    name:"ShopListModel",
    components: {
      ARow,
    },
    data(){
      return{
        description: '选择超市页',
        title: "操作",
        visible: false,
        confirmLoading: false,
        // 查询条件
        queryParam: {},
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: "center",
            customRender: function (t, r, index) {
              return parseInt(index) + 1;
            }
          },
          {
            title: '图片',
            align: "center",
            dataIndex: 'image',
            scopedSlots: {customRender: "avatarslot"}
          },
          {
            title: '超市名称',
            align: "center",
            dataIndex: 'shopName',
          },
          {
            title: '超市地址',
            align: "center",
            dataIndex: 'shopAddress',
          }
        ],
        //数据集
        dataSource: [],
        // 分页参数
        ipagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        isorter: {
          column: 'createTime',
          order: 'desc',
        },
        loading: false,
        shopId:"",
        cids:[],
        options:[],
        fenl:'',
        selectedRowKeys: [],
        selectedRows: [],
        url: {
          list: "/kunze/shop/list",
          imgerver: window._CONFIG['staticDomainURL'],
        },
      }
    },
    created() {
      //this.shopId = localStorage.getItem('shopId');
    },
    methods:{
      getAvatarView: function (avatar) {
        return getFileAccessHttpUrl(avatar,this.url.imgerver,"http")
      },
      shopList(){
        this.visible = true;
        this.loadData(1);
      },
/*      //选择分类
      onChangeShop(e){
        this.cids=e
        this.search.cid3=e[2]
      },*/
      loadData(arg) {
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.list;
            this.ipagination.total = res.result.total;
          }
        })
      },
      handleCancel() {
        this.close();
      },
      close() {
        this.$emit('ok');
        this.visible = false;
      },
      searchReset() {
        var that = this;
        that.queryParam = {};
        that.loadData(1);
      },
      handleOk(){
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条数据！');
          return;
        } else {
          /*  this.$emit('func',this.selectedRows);
            this.selectedRowKeys = [];
            this.selectionRows = [];
            this.close();*/
          console.log(this.selectionRows[0]);
          this.$emit('func',this.selectionRows[0]);
          this.close();
        }
      },
      getQueryParams() {
        var param = Object.assign({}, this.queryParam, this.isorter);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        if(localStorage.getItem('shopType')==3){
          param.area = localStorage.getItem('area')
          // param.shopType = 2
        }
        return filterObj(param);
      },
      getQueryField() {
        //TODO 字段权限控制
        var str = "id,";
        this.columns.forEach(function (value, index) {
          str += "," + value.dataIndex;
        });
        return str;
      },
      onSelectChange(selectedRowKeys, selectionRows) {
        this.selectedRowKeys = selectedRowKeys;
        this.selectionRows = selectionRows;
      },
      onClearSelected() {
        this.selectedRowKeys = [];
        this.selectionRows = [];
      },
      searchQuery(){
        this.loadData(1);
      },
      handleTableChange(pagination, filters, sorter) {
        //分页、排序、筛选变化时触发
        console.log(sorter);
        //TODO 筛选
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
        }
        this.ipagination = pagination;
        this.loadData();
      },
    }
  }
</script>