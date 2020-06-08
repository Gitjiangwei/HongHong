<template>
  <div>
    <a-row>
      <a-col  :xl="18" :lg="18" :md="18" :sm="24" :xs="24">
        <span style="font-weight: bold; font-size: 16px;display: inline-block;margin: 0 0 0 10px">平台信息数据统计</span>
        <a-card style="width: 100%;" :loading="loading">
          <div style="width:100% ;height: 100%">
            <a-row>
              <a-col  style="border-right:2px solid  rgba(246, 246, 246, 1) " :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
              <div class="card-item" >
                <a-col>
                  <div class="img">
                    <img src="../../assets/money.png" alt="" @click="mmm">
                  </div>
                  <div class="font">
                    <div class="font-item" style="line-height: 68px;">月交易额</div>
                    <div class="font-item" style="line-height: 40px; font-size: 26px">{{moneyMoney | NumberFormat}} &nbsp;<span style="color: #D5D6D9;font-size: 12px">元</span> </div>
                  </div>
                </a-col>
              </div>
              </a-col>


              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24" style="border-right:2px solid  rgba(246, 246, 246, 1) ">
              <div class="card-item" >
                <div class="img">
                  <img src="../../assets/commodity.png" alt="">
                </div>
                <div class="font">
                  <div class="font-item" style="line-height: 86px;">商品数量</div>
                  <div class="font-item" style="line-height: 40px;font-size: 26px">{{spuNum | NumberFormat}} &nbsp;<span style="color: #D5D6D9;font-size: 12px">件</span></div>
                </div>
              </div>
              </a-col>
              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
              <div class="card-item">
                <div class="img">
                  <img src="../../assets/dd.png" alt="">
                </div>
                <div class="font">
                  <div class="font-item" style="line-height: 86px;">订单数量</div>
                  <div class="font-item" style="line-height: 40px;font-size: 26px">{{orderNum | NumberFormat}} &nbsp;<span style="color: #D5D6D9;font-size: 12px">单</span></div>
                </div>
              </div>
              </a-col>
            </a-row>

          </div>
        </a-card>





        <span style="font-weight: bold; font-size: 16px;display: inline-block;margin: 20px 0 0 10px">订单统计与仓库统计</span>
        <a-row>
          <a-col  :xl="11" :lg="11" :md="11" :sm="24" :xs="24">
        <a-card :loading="loading" >
          <div class="dd-box">
            <div style="width:100%;font-size:16px;font-weight:400;color:rgba(28,46,50,1);">订单统计</div>
            <div class="dd-box-item">
              <div class="dd-box-item2">
                <div class="dd-box-item2-font">
                  <span @click="handList">{{pending}}</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">待处理订单</span>
                </div>
              </div>
            </div>
            <div class="dd-box-item">
              <div class="dd-box-item2" style="border-right: 0">
                <div class="dd-box-item2-font">
                  <span @click="handListEnd">{{completed}}</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dianOk"></span><span class="font">已完成的订单</span>
                </div>
              </div>
            </div>
          </div>


        </a-card>
          </a-col>
          <a-col :span="2"></a-col>
          <a-col :xl="11" :lg="11" :md="11" :sm="24" :xs="24">
        <a-card :loading="loading" >
          <div class="dd-box">
            <div style="width:100%;font-size:16px;font-weight:400;color:rgba(28,46,50,1);">仓库统计</div>
            <div class="dd-box-item">
              <div class="dd-box-item2">
                <div class="dd-box-item2-font">
                  <span style="color: red;font-weight: bold">{{stock}}</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">库存不足的商品</span>
                </div>
              </div>
            </div>
            <div class="dd-box-item">
              <div class="dd-box-item2" style="border-right: 0">
                <div class="dd-box-item2-font">
                  <span>{{notsheif}}</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dianOk"></span><span class="font">未上架商品</span>
                </div>
              </div>
            </div>
          </div>
        </a-card>

      </a-col>
        </a-row>



        <a-row>
          <a-col :xl="17" :lg="17" :md="24" :sm="24" :xs="24">
            <span style="font-weight: bold; font-size: 16px;display: inline-block;margin: 20px 0 0 10px">最近7日</span>
            <a-card :loading="loading">
              <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>
            </a-card>
          </a-col>
          <a-col :span="1"></a-col>
          <a-col :xl="6" :lg="12" :md="12" :sm="24" :xs="24">
            <span style="font-weight: bold; font-size: 16px;display: inline-block;margin: 20px 0 0 10px">快捷功能入口</span>
            <a-card >
              <div style="height:78px;width:100%;margin: 19px 0 19px 40px">
                <img src="../../assets/sp.png" alt="" style="width:78px;height: 78px">
                &nbsp;
                <span>添加商品</span>
              </div>
              <div style="height:78px;width:100%;margin: 19px 0 19px 40px">
                <img src="../../assets/receiving.png" alt="" style="width:78px;height: 78px">
                &nbsp;
                <span>快速接单</span>
              </div>
              <div style="height:78px;width:100%;margin: 19px 0 19px 40px">
                <img src="../../assets/order.png" alt="" style="width:78px;height: 78px">
                &nbsp;
                <span>订单列表</span>
              </div>
            </a-card>
          </a-col>
        </a-row>
      </a-col>
      <a-col :span="1">
      </a-col>
      <a-col  :xl="4" :lg="12" :md="12" :sm="24" :xs="24">
        <span style="font-weight: bold; font-size: 16px;display: inline-block;margin: 0 0 0 10px">设置配送费</span>
        <a-card style="width: 100%;height: 184px" :loading="loading">
          <div style="margin-bottom: 20px;font-size: 16px;font-weight: bold">当前配送费： {{deliveryFee}}￥</div>
          <a-input-search
            placeholder="请输入配送费"
            enter-button="确认"
            size="large"
            @search="onSearch"
          />
        </a-card>
      </a-col>
    </a-row>
    <order-status-list ref="OrderStatusList"></order-status-list>
  </div>
</template>
<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import {getAction} from '@/api/manage'
  import LineChartMultid from '@/components/chart/LineChartMultid'
  import OrderStatusList from "../order/model/OrderStatusList"
  import { postAction } from '../../api/manage'
  import qs from 'qs'


  export default {
    name:"IndexChartShop",
    components:{
      ChartCard,
      ACol,
      ATooltip,
      LineChartMultid,
      OrderStatusList
    },
    data(){
      return{
        span:8,
        loading: true,
        moneyMoney:"0",
        orderNum:"0",
        spuNum:"0",
        pending:"0",
        completed:"0",
        stock:"0",
        notsheif:"0",
        shopId:"",
        deliveryFee:"",
        visitFields:['成交量'],
        visitInfo:[
          {成交量: 2,
            type: "02-11",
            },
          {成交量: 4,
            type: "03-11",
            },
          {成交量: 6,
            type: "04-11",
            },
          {成交量: 8,
            type: "05-11",
            },
        ],
        url:{
          selectInfo:"/kunze/menu/selectInfo",
          selectOrder:"/kunze/menu/selectOrderstatistics",
          selectStock:"/kunze/menu/selectWarehouseStatistics",
          selectSeven:"/kunze/menu/selectSevenDeal",
        },
        indicator: <a-icon type="loading" style="font-size: 24px" spin />
      }
    },
    created(){
      setTimeout(() => {
        this.loading = !this.loading
      }, 1000);
      let shopId=this.$store.state.shopId;
      this.shopId = shopId;
      this.loaders(shopId);
      this.loaderOrder(shopId);
      this.loaderStock(shopId);
      this.loaderSevenDeal(shopId);
    },
    methods:{
      //查询配送费
      queryonSearch(){
        let param = new URLSearchParams()
        param.append('id',this.shopId)
        param.append('pageNo' , 1)
        param.append('pageSize' , 1)
        postAction('/kunze/shop/queryShops',param).then((res)=>{
          // console.log(res)
          if(res.success==true){
            this.deliveryFee=res.result.list[0].postFree
          }
        })
      },
      //点击设置配送费
      onSearch(e){
        let updateShop={
          postFree:e,
          id:this.shopId
        }
        postAction('/kunze/shop/updateShop',updateShop).then((res)=>{
          // console.log(res)
          if(res.success==true){
            this.deliveryFee=e
            this.$message.success('配送费修改成功');
          }else {
            this.$message.error('配送费修改失败');
          }

        })
      },
      mmm(){

       let winWidth = window.innerWidth;
        console.log(winWidth)
      },
      loaders(shopId){
        let params = {
          shopId:shopId,
        }
        getAction(this.url.selectInfo,params).then((res) => {
            if(res.success){
              this.moneyMoney = res.result.moneyMoney;
              this.orderNum = res.result.orderNum;
              this.spuNum = res.result.spuNum;
            }
        })
      },
      handList(){
        this.$refs.OrderStatusList.handList("2",this.shopId);
        this.$refs.OrderStatusList.title = "待处理订单";
      },
      handListEnd(){
        this.$refs.OrderStatusList.handList("5",this.shopId);
        this.$refs.OrderStatusList.title = "已完成订单";
      },
      loaderOrder(shopId){
        let params = {
          shopId:shopId,
        }
        getAction(this.url.selectOrder,params).then((res) => {
          if(res.success){
              this.pending = res.result.pending;
              this.completed = res.result.completed;
          }
        })
      },
      loaderStock(shopId){
        let params = {
          shopId:shopId,
        }
        getAction(this.url.selectStock,params).then((res) => {
          if(res.success){
            this.notsheif = res.result.notsheif;
            this.stock = res.result.stock;
          }
        })
      },
      loaderSevenDeal(shopId){
        let params = {
          shopId:shopId
        }
        getAction(this.url.selectSeven,params).then((res) => {
          this.visitInfo = [];
          if(res.success){
              for(let i = 0;i<res.result.length;i++){
                 let result = {
                   "成交量":res.result[res.result.length-(i+1)].count,
                   "type":res.result[res.result.length-(i+1)].clickDate
                 }
                  this.visitInfo.push(result);
              }
          }
        })
      }
    },
    mounted () {
      this.queryonSearch()
    }
    // mounted () {
    //   let that = this
    //   window.onresize = () => {
    //     return (() => {
    //       window.screenWidth = window.innerWidth
    //       this.screenWidth = window.screenWidth
    //     })()
    //   }
    // },
    // watch: {
    //   // 监听浏览器窗口宽度,当浏览器窗口小于1325时,隐藏系统时间
    //   screenWidth(val) {
    //     // console.log(val)
    //     if (val < 500) {
    //       this.span=24
    //     } else {
    //       this.span=8
    //     }
    //   }
    // }
  }
</script>

<style lang="less" scoped>
  .card-item{
    /*width: calc((100% - 4px)/3);*/
    height: 136px;
    float: left;
    .img{
      width: 84px;
      height: 84px;
      border-radius: 42px;
      margin:26px 30px 26px 40px;
      float: left;
    }
    .font{
      height: 100%;
      float: left;
      width: calc(100% - 154px);
      .font-item{
        width: 100%;
        height: 50%;
        text-align: left;
        font-weight: bold;
      }
    }
  }
  .dd-box{
    width:538px;
    height:255px;
    .dd-box-item{
      height: 220px;
      width: calc((100% - 1px)/2);
      float: left;
    }
    .dd-box-item2{
      width:100%;
      height:76px;
      border-right: 1px solid rgba(219,219,219,1);
      margin-top: 74px;
      .dd-box-item2-font{
        height: 50%;
        width: 100%;
        font-size:30px;
        font-weight:400;
        color:rgba(28,46,50,1);
        text-align: center;
        line-height: 37px;
        .dian{
          width:8px;
          height:8px;
          background:rgba(91,176,33,1);
          border-radius:4px;
          display: inline-block;
          margin-right: 5px;
        }
        .dianOk{
          width:8px;
          height:8px;
          background:rgba(250,103,0);
          border-radius:4px;
          display: inline-block;
          margin-right: 5px;
        }
        .font{
          font-size:16px;
          font-weight:400;
          color:rgba(28,46,50,1);
        }
      }
    }
  }

</style>