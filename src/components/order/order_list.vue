<template>
  <div class="order">
    <div class="order_title">
      当前订单
    </div>
    <div class="order_body" v-model="order_list">
      <h3>{{order_list.orderSubject}}</h3>
      <p><span class="lab">订单编号:</span><span>{{order_list.orderId}}</span></p>
      <p><span class="lab">预约时间:</span><span>{{order_list.orderTime}}</span></p>
      <p><span class="lab">预约地址:</span><span>{{agentAddress}}</span></p>
      <p><span class="lab">订单价格:</span><span>￥{{order_list.orderMoney}}</span></p>
      <p><span class="lab">订单状态:</span><span>{{order_list.orderState}}</span></p>
			<p><span class="lab" v-if="isRefuse">拒绝缘由:</span><span>{{order_list.rejectReason}}</span></p>
      <div v-if="this.order_state == 1" @click="to_payfor" class="to_check">
        <img src="/static/images/payment.png" class="icon_img"/>去付款</div>
			<div v-if="this.order_state == 2" class="to_wait">等待配单</div>
			<div v-if="this.order_state == 10" class="to_wait">等待代理商确认</div>
      <div v-if="this.order_state == 3" @click="to_checkcar" class="to_check">配单成功,去检车</div>
      <div v-if="this.order_state == 4" @click="to_evaluate" class="to_check">完成检车</div>
      <div v-if="this.order_state>0 && this.order_state < 5" @click="to_cancel" class="to_cancel">取消订单</div>
    </div>
		<mu-dialog title="提示信息" width="360" :open.sync="openSimple">
			检车是否已完成？
			<mu-button slot="actions" flat @click="closeSimpleDialog">取消</mu-button>
			<mu-button slot="actions" flat color="primary" @click="goSuccess">确定</mu-button>
		</mu-dialog>
		
		<mu-dialog title="提示信息" width="360" :open.sync="openSimple3">
			{{msg}}
			<mu-button slot="actions" flat color="primary" @click="closeSimpleDialog">关闭</mu-button>
		</mu-dialog>
		
		<mu-dialog title="提示信息" width="360" :open.sync="openSimple2">
			确定取消订单吗?
			<mu-button slot="actions" flat @click="closeSimpleDialog2">取消</mu-button>
			<mu-button slot="actions" flat color="primary" @click="goSuccess2">确定</mu-button>
		</mu-dialog>
		
		<div class="alert-demo-wrapper">
			<mu-alert color="warning" @delete="alert1 = false" delete v-if="alert1" transition="mu-scale-transition">
				<mu-icon left></mu-icon>没有订单啊，快去下单吧~
			</mu-alert>
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
				alert1: false,
				isRefuse: false,
				openSimple: false,
				openSimple2: false,
				openSimple3: false,
				msg: '',
        order_list: {},
        agentAddress: "",
        order_state: 0
      }
    },
    created() {
      this.$ajax.get("/check-car/app/check/userOrders?type=0", {}).then((res) => {
				if(res.data.code == 500){
					this.alert1=true
				}
        else if (res.data.code == 200) {
					console.log(res.data)
            this.order_list = res.data.data[0].orderEntity;
            let alipayNotifyEntity = res.data.data[0].alipayNotifyEntity;
            this.agentAddress = res.data.data[0].agentEntity.agentAddress;
            this.order_state = res.data.data[0].orderEntity.orderState;
						switch (this.order_list.orderState) {
							case 1:
								this.order_list.orderState = "下单成功";
								break;
							case 2:
								this.order_list.orderState = "付款成功";
								break;
							case 3:
								this.order_list.orderState = "配单成功";
								break;
							case 4:
								this.order_list.orderState = "已开始检车";
								break;
							case 5:
								this.order_list.orderState = "订单完成";
								break;
							case 6:
								this.order_list.orderState = "评论完成";
								break;
							case 7:
								this.order_list.orderState = "订单拒绝";
								this.isRefuse=true;
								break;
							case 10:
								this.order_list.orderState = "订单修改，等待代理商确认";
								break;
							default :
								this.order_list.orderState = '未知'
						}
          }
      });
    },
    methods:{
      to_payfor(){
        this.$router.push({name:'order',query:{orderId: this.order_list.orderId}})
      },
      to_checkcar(){
        this.$router.push({name:'check_car',query:{orderId: this.order_list.orderId}})
      },to_evaluate(){
				this.openSimple=true
				this.msg= '检车是否已完成？'
			},closeSimpleDialog(){
				this.openSimple=false
				this.openSimple3=false
			},goSuccess(){
				this.openSimple=false
				this.$router.push({name:'evaluate',query:{orderId: this.order_list.orderId}})
			},to_cancel(){
				this.openSimple2=true
			},closeSimpleDialog2(){
				this.openSimple2=false
			},goSuccess2(){
				this.openSimple2=false
				this.$ajax.get("/check-car/app/check/cancelOrder?orderId="+this.order_list.orderId, {
				}).then((res) => {
					if(res.data.code == 200 || res.data.code == 500){
						this.msg= res.data.msg
					}else{
						this.msg= '未知异常!'
					}
					this.openSimple3=true
				})
				
			},toggleAlert () {
				this.alert1 = !this.alert1;
			}
    }
  }
</script>

<style scoped>
  .order_title {
    line-height: 60px;
    text-align: center;
    background-color: #dddddd;
  }
  .order_body h3{
    text-align: center;
    padding: 0 10px;
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

  .to_check {
    margin: 20px auto 0;
    width: 90%;
    line-height: 60px;
    background-color: #00bcd4;
    text-align: center;
    color: #fafafa;
    font-size: 16px;
  }
	.to_wait {
		margin: 20px auto 0;
		width: 90%;
		line-height: 60px;
		background-color: #00bcd4;
		text-align: center;
		color: #DC143C;
		font-size: 16px;
	}
	.icon_img{
		margin-right: 10px;
		width: 20px;
		height: 20px;
    vertical-align: middle;
	}
  .to_cancel{
    margin: 20px auto 0;
    width: 90%;
    line-height: 60px;
    border: 1px solid #666;
    text-align: center;
    color: #666;
    font-size: 16px;
  }
	
.alert-demo-wrapper {
  width: 100%;
  > .mu-alert {
    margin-bottom: 16px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  > .mu-button {
    margin: 0 auto;
    display: block;
  }
}

.mu-scale-transition-enter-active,
.mu-scale-transition-leave-active {
  transition: transform .45s cubic-bezier(0.23, 1, 0.32, 1), opacity .45s cubic-bezier(0.23, 1, 0.32, 1);
  backface-visibility: hidden;
}

.mu-scale-transition-enter,
.mu-scale-transition-leave-active {
  transform: scale(0);
  opacity: 0;
}
</style>
