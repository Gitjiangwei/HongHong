<template>
  <a-modal
    :title="title"
    :width="1600"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @cancel="handleCancel"
    cancelText="关闭"
  >
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="24">
            <a-form-item label="订单编号">
              <a-input placeholder="请输入订单编号" v-model="queryParam.orderId"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="24">
            <a-form-item label="订单状态">
              <a-select placeholder="请输入订单状态"  v-model="queryParam.status">
                <a-select-option value="1">未付款</a-select-option>
                <a-select-option value="2">已付款</a-select-option>
                <a-select-option value="3">未发货</a-select-option>
                <a-select-option value="4">已发货</a-select-option>
                <a-select-option value="7">已退款</a-select-option>
                <a-select-option value="5">交易成功</a-select-option>
                <a-select-option value="6">交易关闭</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="24" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
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
        <span slot="action" slot-scope="text, record">
          <a @click="handleDetail(record)">查看</a>
          <span v-if="record.status==2">
            <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">是否接单 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                     <a @click="preview(record.orderId)">接单</a>
              </a-menu-item>
              <a-menu-item>
                  <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.orderId)">
                    <a>拒绝</a>
                  </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
          </span>
        </span>
      </a-table>
    </div>
    <order-detail ref="OrderDetail"></order-detail>
  </a-modal>
</template>
<script>
  import ARow from "ant-design-vue/es/grid/Row";
  import { JeecgListMixin } from '@/mixins/JeecgListMixin';
  import {deleteAction, getAction, postAction} from '@/api/manage';
  import {filterObj,timeFix} from '@/utils/util';
  import OrderDetail from './OrderDetail';
  import $ from 'jquery';

  export default {
    name: "OrderStatusList",
    mixins:[JeecgListMixin],
    components:{
      ARow,
      OrderDetail,
    },
    data() {
      return {
        description: '订单',
        status:"",
        shopId:"",
        visible: false,
        confirmLoading: false,
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
            title: '订单编号',
            align: "center",
            dataIndex: 'orderId'
          },
          {
            title: '实付金额',
            align: "center",
            dataIndex: 'payment'
          },
          {
            title: '收货人',
            align: "center",
            dataIndex: 'consigneeSex',
          },
          {
            title: '联系方式',
            align: "center",
            dataIndex: 'telphone'
          },
          {
            title: '下单时间',
            align: "center",
            dataIndex: 'createTime'
          },
          {
            title: '订单状态',
            align: "center",
            dataIndex: 'status',
            customRender: (text) => {
              if(text==1){
                return " 未付款";
              }else if(text==2){
                return <span style='color: green; font-weight:bold'>已付款</span>;
              }else if(text==3){
                return <span style='color: red;font-weight:bold'>未发货</span>;
              }else if(text==4){
                return "已发货";
              }else if(text==5){
                return "交易成功";
              }else if(text==6){
                return "交易关闭";
              }else if(text==7){
                return "已退款";
              }else {
                return text;
              }
            }
          },
          {
            title: '交易完成时间',
            align: "center",
            dataIndex: 'endTime'
          },
          {
            title: '提货方式',
            align: "center",
            dataIndex: 'pickUp',
            customRender: (text) => {
              if(text==1){
                return "自提";
              }else if(text==2){
                return "商家配送";
              }else{
                return text;
              }
            }
          },
          {
            title: '备注',
            align: "center",
            dataIndex: 'buyerMessage'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            scopedSlots: {customRender: 'action'},
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
        url: {
          list: "/kunze/order/selectOrder",
          dayin:"/kunze/imidate/dayin",
          jiedan:"/kunze/order/selectStatus",
        },
      }
    },
    created(){
      this.shopId=this.$store.state.shopId
    },
    methods:{
      loadData(arg) {
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件

        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            debugger;
            this.dataSource = res.result.list;
            this.ipagination.total = res.result.total;
          }
        })
      },
      handList(status,shopId){
        this.visible = true;
        this.status = status;
        this.shopId = shopId;
        this.loadData(1);
      },
      handleDetail(record) {
        this.$refs.OrderDetail.detail(record);
        this.$refs.OrderDetail.title = "订单详情";
      },
      searchReset() {
        var that = this;
        that.queryParam = {}
        that.loadData(1);
      },
      getQueryParams() {
        var param = Object.assign({}, this.queryParam, this.isorter);
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        param.shopId = this.shopId;
        param.status = this.status;
        return filterObj(param);
      },
      onSelectChange(selectedRowKeys, selectionRows) {
        this.selectedRowKeys = selectedRowKeys;
        this.selectionRows = selectionRows;
      },
      handleCancel() {
        this.close();
      },
      close() {
        this.$emit('ok');
        this.visible = false;
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
    },
  }


</script>