<template>
    <div class="order">
      <div class="order_title">
        订单详情
      </div>
      <div class="order_body" v-model="order_list">
        <p><span class="lab">订单编号:</span><span>{{order_list.orderId}}</span></p>
        <p><span class="lab">订单主题:</span><span>{{order_list.orderSubject}}</span></p>
        <p><span class="lab">预约时间:</span><span>{{order_list.orderTime}}</span></p>
        <p><span class="lab">订单价格:</span><span>{{order_list.orderMoney}}</span></p>
      </div>
      <div class="go_charge" @click="go_charge" >
        确定付款
      </div>

    </div>
</template>

<script>

    export default {
        name: "order",
      data(){
          return{
            order_list:{

            },
          }
      },
      methods:{
        go_charge(){
					
					console.log( this.order_list)
					console.log("orderId:"+ this.order_list.orderId)
					
					window.open("http://localhost:8080"+"/check-car/app/alipay/goAlipay/"+this.order_list.orderId)
					
					window.location.href="http://localhost:8080"+"/check-car/app/alipay/goAlipay/"+this.order_list.orderId
					
          this.$ajax.get("/check-car/app/alipay/goAlipay/"+this.order_list.orderId, {
          }).then((res)=> {
            if (res.data.code == 200) {

            }
          });
        }
      },
      created(){
        this.$ajax.get("/check-car/app/check/userOrders", {
        }).then((res)=> {
          if (res.data.code == 200) {
						
              this.order_list = res.data.data[0].orderEntity;
          }
        });
      }
    }
</script>

<style scoped>
.order_title{
  line-height: 60px;
  text-align: center;
  background-color: #dddddd;
}
.order_body p{
  padding: 0 15px;
}
.lab{
  display: inline-block;
  width: 80px;
  margin-right: 20px;
  text-align: right;
}
.go_charge{
  margin: 0 auto;
  width: 80%;
  height: 60px;
  text-align: center;
  line-height: 60px;
  color: #222222;
  font-size: 16px;
  background-color: #00bcd4;
}
</style>
