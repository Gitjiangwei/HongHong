<template>
  <div>
    <a-row>
      <a-col :span="18">
        <span>平台数据信息管理</span>
        <a-card style="width: 100%;">
          <div style="width: 100%;height: 100%">
            <div class="card-item">
              <div class="img">
                <img src="../../assets/dd.png" alt="">
              </div>
              <div class="font">
                <div class="font-item" style="line-height: 86px;">月交易额</div>
                <div class="font-item" style="line-height: 40px;">3656 </div>
              </div>
            </div>
            <div class="card-item2"></div>
            <div class="card-item">123</div>
            <div class="card-item2"></div>
            <div class="card-item">123</div>
          </div>
        </a-card>
        <span>订单统计与仓库统计</span>
        <a-row>
          <a-col :span="11">
        <a-card >
          <div class="dd-box">
            <div style="width:100%;font-size:16px;font-weight:400;color:rgba(28,46,50,1);">订单统计</div>
            <div class="dd-box-item">
              <div class="dd-box-item2">
                <div class="dd-box-item2-font">
                  <span>1354</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">待处理订单</span>
                </div>
              </div>
            </div>
            <div class="dd-box-item">
              <div class="dd-box-item2" style="border-right: 0">
                <div class="dd-box-item2-font">
                  <span>1354</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">待处理订单</span>
                </div>
              </div>
            </div>
          </div>


        </a-card>
          </a-col>
          <a-col :span="2"></a-col>
          <a-col :span="11">
        <a-card >
          <div class="dd-box">
            <div style="width:100%;font-size:16px;font-weight:400;color:rgba(28,46,50,1);">订单统计</div>
            <div class="dd-box-item">
              <div class="dd-box-item2">
                <div class="dd-box-item2-font">
                  <span>1354</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">待处理订单</span>
                </div>
              </div>
            </div>
            <div class="dd-box-item">
              <div class="dd-box-item2" style="border-right: 0">
                <div class="dd-box-item2-font">
                  <span>1354</span>
                </div>
                <div class="dd-box-item2-font">
                  <span class="dian"></span><span class="font">待处理订单</span>
                </div>
              </div>
            </div>
          </div>
        </a-card>

      </a-col>
        </a-row>
        <span>订单统计与仓库统计</span>
        <a-row>
          <a-col :span="17">
            <a-card >
              <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>
            </a-card>
          </a-col>
          <a-col :span="1"></a-col>
          <a-col :span="4">
            <a-card >
              <div style="height:78px;width:100%">
                <img src="../../assets/sp.png" alt="" style="width:78px;height: 78px">
                <span>添加商品</span>
              </div>
              <div style="height:78px;width:100%">
                <img src="../../assets/sp.png" alt="" style="width:78px;height: 78px">
                <span>添加商品</span>
              </div>
              <div style="height:78px;width:100%">
                <img src="../../assets/sp.png" alt="" style="width:78px;height: 78px">
                <span>添加商品</span>
              </div>
            </a-card>
          </a-col>
        </a-row>
      </a-col>
      <a-col :span="4">

      </a-col>
    </a-row>

  </div>
</template>
<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import {getAction} from '@/api/manage'
  import LineChartMultid from '@/components/chart/LineChartMultid'


  export default {
    name:"IndexChartShop",
    components:{
      ChartCard,
      ACol,
      ATooltip,
      LineChartMultid
    },
    data(){
      return{
        visitFields:['ip','visit'],
        visitInfo:[
          {ip: 2,
            tian: "2020-02-11",
            type: "02-11",
            visit: 2},
          {ip: 4,
            tian: "2020-03-11",
            type: "03-11",
            visit: 42},
          {ip: 6,
            tian: "2020-04-11",
            type: "04-11",
            visit: 81},
          {ip: 8,
            tian: "2020-05-11",
            type: "05-11",
            visit: 60},
        ],
      }


    }
  }
</script>

<style lang="less" scoped>
  .card-item{
    width: calc((100% - 4px)/3);
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

      }
    }
  }
  .card-item2{
    width: 2px;
    height: 60px;
    background: rgba(246, 246, 246, 1);
    margin-top: 38px;
    float: left;
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
        .font{
          font-size:16px;
          font-weight:400;
          color:rgba(28,46,50,1);
        }
      }
    }
  }

</style>