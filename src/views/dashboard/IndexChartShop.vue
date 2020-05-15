<template>
  <div class="page-header-index-wide">
    <a-row :gutter="24">
      <a-col :sm="24" :md="6" :xl="18" :style="{ marginBottom: '24px' }">
        <a-col :sm="24" :md="6" :xl="8" :style="{ marginBottom: '24px' }">
          <chart-card :loading="loading" title="总销售额" :total="totalSpu | NumberFormat">
            <a-tooltip title="指标说明" slot="action">
              <a-icon type="info-circle-o" />
            </a-tooltip>
            <template slot="footer">今日销售额<span>￥ 234,444</span></template>
          </chart-card>
        </a-col>
       <a-col :sm="24" :md="6" :xl="8" :style="{ marginBottom: '24px' }">
          <chart-card :loading="loading" title="订单量" :total="totalOrder | NumberFormat">
            <a-tooltip title="指标说明" slot="action">
              <a-icon type="info-circle-o" />
            </a-tooltip>
            <div>
              <mini-area :dataSource="sourceData"/>
            </div>
            <template slot="footer">日订单量<span> {{ todaysOrder | NumberFormat }}</span></template>
          </chart-card>
        </a-col>
        <a-col :sm="24" :md="6" :xl="8" :style="{ marginBottom: '24px' }">
          <chart-card :loading="loading" title="订单量" :total="totalOrder | NumberFormat">
            <a-tooltip title="指标说明" slot="action">
              <a-icon type="info-circle-o" />
            </a-tooltip>
            <div>
              <mini-area :dataSource="sourceData"/>
            </div>
            <template slot="footer">日订单量<span> {{ todaysOrder | NumberFormat }}</span></template>
          </chart-card>
        </a-col>
      </a-col>
    </a-row>
  </div>
</template>
<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import {getAction} from '@/api/manage'

  export default {
    name:"IndexChartShop",
    components:{
      ChartCard,
      ACol,
      ATooltip
    },
    data:{
      loading: true,
      center: null,
      totalOrder:"0",
      totalSpu:"0",
      isDownUp: "down",
      isDownUps: "down",
      loginfo:{},
      visitFields:['ip','visit'],
      visitInfo:[],
    }
  }
</script>

<style lang="less" scoped>
  .circle-cust{
    position: relative;
    top: 28px;
    left: -100%;
  }
  .extra-wrapper {
    line-height: 55px;
    padding-right: 24px;

    .extra-item {
      display: inline-block;
      margin-right: 24px;

      a {
        margin-left: 24px;
      }
    }
  }

  /* 首页访问量统计 */
  .head-info {
    position: relative;
    text-align: left;
    padding: 0 32px 0 0;
    min-width: 125px;

    &.center {
      text-align: center;
      padding: 0 32px;
    }

    span {
      color: rgba(0, 0, 0, .45);
      display: inline-block;
      font-size: .95rem;
      line-height: 42px;
      margin-bottom: 4px;
    }
    p {
      line-height: 42px;
      margin: 0;
      a {
        font-weight: 600;
        font-size: 1rem;
      }
    }
  }
</style>