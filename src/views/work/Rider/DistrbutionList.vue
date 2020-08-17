<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="4" :sm="24">
            <a-form-item label="订单号">
              <a-input placeholder="请输入订单号" v-model="queryParam.orderId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="24">
            <a-form-item label="骑手名称">
              <a-input placeholder="请输入骑手名称" v-model="queryParam.riderName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="24">
            <a-form-item label="超市">
              <a-input placeholder="请选择超市"  :value ="shopName"  @click="handleShopList" />
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="24">
            <a-form-item label="是否结算">
              <a-select placeholder="是否结算"  v-model="queryParam.settlement">
                <a-select-option value="0">未结算</a-select-option>
                <a-select-option value="1">已结算</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="4" :sm="24" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>


    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">
        <!-- 状态渲染模板 -->
        <template slot="customRenderStatus" slot-scope="settlement">
          <a-tag v-if="settlement==0" color="green">未结算</a-tag>
          <a-tag v-if="settlement==1" color="orange">已结算</a-tag>
        </template>


        <!-- 字符串超长截取省略号显示-->
        <span slot="description" slot-scope="text">
          <j-ellipsis :value="text" :length="20" />
        </span>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">结算</a>

<!--          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>-->
        </span>

      </a-table>
    </div>
    <shop-list-model ref="ShopListModel" @func="modalFormOkShop"></shop-list-model>
  </a-card>
</template>
<script>
  import ARow from "ant-design-vue/es/grid/Row";
  import { JeecgListMixin } from '@/mixins/JeecgListMixin';
  import {deleteAction, getAction, postAction,getFileAccessHttpUrl} from '@/api/manage';
  import {filterObj,timeFix} from '@/utils/util';
  import JEllipsis from "@/components/jeecg/JEllipsis";
  import ShopListModel from '../Supermarket/modules/ShopListModel';

  export default {
    name:"DistrbutionList",
    components:{
      JeecgListMixin,
      ARow,
      JEllipsis,
      ShopListModel
    },
    data(){
      return{
        description: '配送信息页面',
        loading: false,
        // 查询条件
        queryParam: {},
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title: '订单编号',
            align:"center",
            dataIndex: 'orderId',
          },
          {
            title: '配送地址',
            align:"center",
            dataIndex: 'address',
            scopedSlots: {customRender: "description"}
          },
          {
            title: '配送费',
            align:"center",
            dataIndex: 'deliveryFee',
          },
          {
            title: '骑手名称',
            align:"center",
            dataIndex: 'riderName'
          },
          {
            title: '取货超市',
            align:"center",
            dataIndex: 'shopName',
          },
          {
            title: '取货码',
            align:"center",
            dataIndex: 'pickNo',
          },
          {
            title: '是否结算',
            align:"center",
            dataIndex: 'settlement',
            scopedSlots: { customRender: 'customRenderStatus' },
            filterMultiple: false,
/*            filters: [
              { text: '未结算', value: '0' },
              { text: '已结算', value: '1' },
            ]*/
          },
          {
            title: '发布时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title: '结算时间',
            align:"center",
            dataIndex: 'settlementTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
        //数据集
        dataSource: [],
        // 分页参数
        ipagination: {
          current: 1,
          pageSize: 30,
          pageSizeOptions: ['20', '30', '40'],
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
        shopId:"",
        selectedRowKeys: [],
        selectedRows: [],
        url:{
          list:"/kunze/dist/qeruyDistList",
        },
      }
    },
    created() {
      this.loadData();
    },
    methods:{
      //结算
      handleEdit(e){
        console.log(e)


        deleteAction('/kunze/dist/editDist',{ids:e.id}).then(res=>{
          console.log(res)
        })
      },

      handleShopList(){
        this.$refs.ShopListModel.title = "选择超市";
        this.$refs.ShopListModel.shopList();
      },
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
      modalFormOkShop(data){
        debugger;
        this.shopId = data.id;
        //this.model.shopName = data.id;
        this.shopName = data.shopName;
      },
      modalFormOk() {
        // 新增/修改 成功时，重载列表
        this.loadData();
      },
      searchReset() {
        var that = this;
        that.queryParam = {}
        that.loadData(1);
      },
      getQueryParams() {
        var param = Object.assign({}, this.queryParam, this.isorter);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        //param.settlement = this.filters.settlement
        param.shopId = this.shopId;
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

        if(filters.settlement){
          this.filters.settlement = filters.settlement[0];
        }

        this.ipagination = pagination;
        this.loadData();
      },
    }
  }
</script>