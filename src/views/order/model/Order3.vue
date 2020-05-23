<template>

  <div>
    <a-card :bordered="false">
      <a-row>
        <a-col :span="24" style="padding-left: 20px;" >
          <ul>
            <li v-for="(records,i) in dataSource">
              <span >{{records.sendTime}}</span>
              <span style="margin-left: 25px">{{records.sendEvent}}</span>
            </li>
          </ul>
        </a-col>
      </a-row>
      <a-row>
      <a-col :span="8">
      </a-col>
      <a-col :span="8">
      </a-col>
      <a-col :span="8">
        <a-button style="margin-left: 8px" @click="prevStep">上一步</a-button>
      </a-col>
    </a-row>
    </a-card>
  </div>
</template>
<script>


  import ARow from "ant-design-vue/es/grid/Row";
  import ACol from "ant-design-vue/es/grid/Col";
  import {getAction} from '@/api/manage';


  export default {
    name:"Order3",
    components: {ACol, ARow},
    props:["orderId"],
    data(){
      return{
        orderId:this.orderId,
        dataSource:[],
        url:{
          list:"kunze/order/queryOrderRecord"
        }
      }
    },
    created() {
      this.loader();
    },
    watch:{
      orderId: {
        handler() {
          this.loader();
        },
      }
    },
    methods:{
      prevStep() {
        this.$emit('prevStep', this.userList);
      },
      loader(){
        let params = {
          orderId:this.orderId,
        }
        getAction(this.url.list,params).then((res) =>{
          if(res.success){
            for(let i = 0;i<res.result.length;i++){
                let record = {
                    sendTime:res.result[i].sendTime,
                    sendEvent:res.result[i].sendEvent
                }
                this.dataSource.push(record);
            }
          }
        })
      }
    }
  }
</script>
<style>
  ul li{
    list-style-type: none;
  }
  span{
    font-weight: bold;
  }
</style>