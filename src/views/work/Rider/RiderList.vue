<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="24">
            <a-form-item label="骑手名称">
              <a-input placeholder="请输入骑手名称" v-model="queryParam.riderName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="24">
            <a-form-item label="联系方式">
              <a-input placeholder="请输入联系方式" v-model="queryParam.telphone"></a-input>
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
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>




    <!--    棋手结算弹出框-->
    <a-modal
      title="选择时间"
      :visible="addvisible"
      :confirm-loading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <a-form :form="form" :label-col="{ span: 6 }" :wrapper-col="{ span: 12 }">
        <a-form-item label="选择时间段" hasFeedback>
          <a-range-picker
            :ranges="{ Today: [moment(), moment()], 'This Month': [moment(), moment().endOf('month')] }"
            show-time
            format="YYYY/MM/DD HH:mm:ss"
            @change="onChange"
          />

        </a-form-item>

      </a-form>
    </a-modal>


<!--    骑手结算明细弹出框-->
    <a-modal
      title="明细"
      :visible="addvisiblerider"
      :confirm-loading="confirmLoadingrider"
      @ok="handleOkrider"
      @cancel="handleCancelrider"
    >

      <a-table :columns="columnsrider" :data-source="dataSourcerider">
      </a-table>



    </a-modal>

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

        <!-- 字符串超长截取省略号显示-->
        <span slot="description" slot-scope="text">
          <j-ellipsis :value="text" :length="20" />
        </span>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>

                <a-menu-item>
                <a-popconfirm title="骑手结算?" @confirm="() => jiesuan(record.id)">
                  <a>结算</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>
    <rider-model ref="RiderModel" @ok="modalFormOk"></rider-model>
  </a-card>
</template>
<script>

  import ARow from "ant-design-vue/es/grid/Row";
  import { JeecgListMixin } from '@/mixins/JeecgListMixin';
  import {deleteAction, getAction, postAction,getFileAccessHttpUrl} from '@/api/manage';
  import {filterObj,timeFix} from '@/utils/util';
  import JEllipsis from "@/components/jeecg/JEllipsis";
  import RiderModel from './modules/RiderModel'
  import pick from 'lodash.pick'
  import moment from 'moment'


  export default {
    name:"RiderList",
    mixins:[JeecgListMixin],
    components:{
      ARow,
      JEllipsis,
      RiderModel
    },
    data(){
      return{
        dataSourcerider:[],
        riderId:"",
        timeArry:"",
        dateFormat: 'YYYY/MM/DD',
        monthFormat: 'YYYY/MM',
        addvisible:false,
        addvisiblerider:false,
        confirmLoading:false,
        confirmLoadingrider:false,
        model:{},
        form: this.$form.createForm(this),
        description: '骑手管理页面',
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
            title: '骑手名称',
            align:"center",
            dataIndex: 'riderName',
          },
          {
            title: '联系方式',
            align:"center",
            dataIndex: 'telphone',
          },
          {
            title: '初始密码',
            align:"center",
            dataIndex: 'password',
          },
          {
            title: '身份证号码',
            align:"center",
            dataIndex: 'idenitiy',
          },

          {
            title: '负责地区',
            align:"center",
            dataIndex: 'address',
            scopedSlots: {customRender: "description"}
          },
          {
            title: '创建时间',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title: '最后操作时间',
            align:"center",
            dataIndex: 'updateTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
        columnsrider: [
          {
            title: '骑手名称',
            align:"center",
            dataIndex: 'riderName',
          },
          {
            title: '接单数',
            align:"center",
            dataIndex: 'orderCount'
          },
          {
            title: '结算金额',
            align:"center",
            dataIndex: 'sendPrice'
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
        selectedRowKeys: [],
        selectedRows: [],
        url:{
          list:"/kunze/rider/queryRider",
          delete:"/kunze/rider/delRider",
        },
      }
    },
    created() {
      this.loadData();
    },
    methods:{
      moment,
      onChange(date, dateString){

        this.timeArry=dateString
      },
      jiesuan(e){
        this.riderId=e
        this.addvisible=true
      },

      handleOk(){
        this.timeArry.forEach(e=>{
          this.timeArry[0]=this.timeArry[0].replace('/', '-')
          this.timeArry[1]=this.timeArry[1].replace('/', '-')


        })
        console.log(this.timeArry)
        getAction('/kunze/riders/send/queryRiderAccount',{riderId:this.riderId,startTime:this.timeArry[0],endTime:this.timeArry[1]}).then(e=>{
          console.log(e)
          debugger
          if(e.success==true){
            if(e.result.riderId!=null){
              this.dataSourcerider=[]
              this.dataSourcerider.push(e.result)
              this.addvisiblerider=true
              this.$message.success('结算成功');
              this.addvisible=false
            }else {
              this.$message.warning('暂无可结算订单');
            }



          }else {
            this.$message.error('结算失败');
          }

        })


      },
      handleCancel(){
        this.addvisible=false
      },
      handleCancelrider(){
        this.addvisiblerider=false
      },
      handleOkrider(){
        this.addvisiblerider=false

      },
      loadData(arg) {
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        getAction(this.url.list, params).then((res) => {
          debugger
          if (res.success) {
            this.dataSource = res.result.list;
            this.ipagination.total = res.result.total;
          }
        })
      },
      handleAdd:function(){
        this.$refs.RiderModel.add();
        this.$refs.RiderModel.title = "新增骑手信息";
      },
      handleEdit:function(record){
/*       let opens=[]
        opens.push(record.province)
        opens.push(record.city)
        opens.push(record.area)*/
        this.$refs.RiderModel.edit(record);
        this.$refs.RiderModel.title = "修改骑手信息";
        //this.$refs.SuperModules.optionss = opens;
      },
      handleDelete:function(ids){
        let that = this;
        deleteAction(this.url.delete,{ids:ids}).then((res) =>{
          if(res.success){
            that.$message.success(res.message);
            that.loadData();
            that.onClearSelected();
          }else {
            that.$message.warning(res.message);
          }
        })
      },
      //批量删除
      batchDel:function(){
        if (this.selectedRowKeys.length <= 0) {
          this.$message.warning('请选择一条记录！');
          return;
        }else {
          var ids = "";
          for (var a = 0; a < this.selectedRowKeys.length; a++) {
            ids += this.selectionRows[a].id + ",";
          }
          let that = this;
          // debugger;
          this.$confirm({
            title:"确认删除",
            content:"是否确认删除选中数据！",
            onOk:function () {
              deleteAction(that.url.delete,{ids:ids}).then((res) =>{
                if(res.success){
                  that.$message.success(res.message);
                  that.loadData();
                  that.onClearSelected();
                }else {
                  that.$message.warning(res.message);
                }
              })
            }
          })
        }

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
        param.isFlag = this.filters.isFlag;
        if(localStorage.getItem('shopType')==3) {
          param.area = localStorage.getItem('area')
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
        this.filters.isFlag = filters.isFlag[0];
        this.ipagination = pagination;
        this.loadData();
      },
    }
  }
</script>