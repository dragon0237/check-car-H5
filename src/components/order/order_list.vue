<template>
  <div class="order">
    <div class="order_title">
      订单列表
    </div>
    <div class="order_body" v-model="order_list">
      <p><span class="lab">订单编号:</span><span>{{order_list.orderId}}</span></p>
      <p><span class="lab">预约时间:</span><span>{{order_list.orderTime}}</span></p>
      <p><span class="lab">订单价格:</span><span>{{order_list.orderMoney}}</span></p>
      <p><span class="lab">预约地址:</span><span>{{agentAddress}}</span></p>
      <p><span class="lab">订单状态:</span><span>{{order_list.orderState}}</span></p>
    </div>
    <foot-nav></foot-nav>
  </div>
</template>

<script>
  import footNav from '../../components/common/foot'
  export default {
    name: "order_list",
    components: {
      footNav
    },
    data() {
      return {
        order_list: {},
        agentAddress: ""
      }
    },
    created(){
      this.$ajax.get("/check-car/app/check/userOrders", {
      }).then((res)=> {
        if (res.data.code == 200) {
          this.order_list = res.data.data[0].orderEntity;
          this.agentAddress = res.data.data[0].agentEntity.agentAddress;
          switch (this.order_list.orderState) {
            case 1:this.order_list.orderState = "下单成功";
              break;
            case 2:this.order_list.orderState = "配单成功";
              break;
            case 3:this.order_list.orderState = "订单拒绝";
              break;
            case 4:this.order_list.orderState = "开始检车";
              break;
            case 5:this.order_list.orderState = "订单完成";
              break;
            case 6:this.order_list.orderState = "订单处理失败";
              break;
            case 7:this.order_list.orderState = "评论完成";
              break;
            default :this.order_list.orderState = ''
          }

        }
      });
    }
  }
</script>

<style scoped>
  .order_title {
    line-height: 60px;
    text-align: center;
    background-color: #dddddd;
  }

  .order_body p {
    padding: 0 15px;
  }

  .lab {
    display: inline-block;
    width: 80px;
    margin-right: 20px;
    text-align: right;
  }

</style>
