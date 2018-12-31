 <template>
  <mu-container>
    <div class="font_pic">
      <input class="fileInput" type="file" id="avater" name="file" accept="image/png,image/gif,image/jpeg" @change="update" />
      <img :src="headpic" id="ex_img">
    </div>
    <mu-form :model="form" class="mu-demo-form" label-position="left" label-width="100">
      <mu-flex class="select-control-row">
        <mu-checkbox v-model="checkbox" :label="'运营车：状态选择'"></mu-checkbox>
      </mu-flex>
      <mu-form-item prop="select" label="车辆类型">
        <mu-select v-model="form.carType">
          <mu-option v-for="(index,item) in options" :key="index" :label="index" :value="item"></mu-option>
        </mu-select>
      </mu-form-item>
      <mu-form-item prop="input" label="车辆号牌">
        <mu-text-field v-model="form.carId"></mu-text-field>
      </mu-form-item>
      <mu-form-item prop="input" label="发动机号">
        <mu-text-field v-model="form.engineId"></mu-text-field>
      </mu-form-item>
      <mu-form-item prop="date" label="注册日期">
        <mu-date-input v-model="form.registerTime" container="dialog"  full-width></mu-date-input>

      </mu-form-item>
      <mu-form-item prop="input" label="联系手机">
        <mu-text-field v-model="form.call"></mu-text-field>
      </mu-form-item>
      <!--<mu-form-item prop="input" label="验证码">
        <mu-text-field v-model="form.smscode"></mu-text-field>
      </mu-form-item>-->
      <mu-button class="nextBtn" @click="to_next" color="primary">下一步</mu-button>
    </mu-form>
    <div class="suporse">
      技术支持：北京道火自然科技
    </div>
    <mu-dialog title="提示信息" width="360" :open.sync="openSimple">
      {{msg}}
      <mu-button slot="actions" flat color="primary" @click="closeSimpleDialog">Close</mu-button>
    </mu-dialog>
  </mu-container>
</template>

<script>
    export default {
        name: "app_form",
      data () {
        return {
          options: {
            1:'微型车',
            2:'小型车',
            3:'中型车',
            4:'大型车'
          },
          msg:"",
          openSimple: false,
          labelPosition: 'left',
          form: {
            carType: '',
            smscode:'',
            carId: '',
            engineId: '',
            registerTime: '',
            call: ''
          },
          checkbox: true,
          headpic: '../../../static/aui/image/demo4.png'
        }
      },
      methods: {
        to_next(){
          this.$ajax.post("/check-car/app/check/user/addUserCar", {
            "carType": this.form.carType,
            "carId": this.form.carId,
            "engineId": this.form.engineId,
            "registerTime": this.form.registerTime,
            "operateCar": (this.checkbox == true? 1: 0),
            "call": this.form.call
          }).then((res)=> {
            if (res.data.code ==200){
              this.$router.push({name:'agent'})
            }else if (res.data.code == 500){
              this.openSimple = true;
              this.msg = res.data.msg
            }
          });
        },
        closeSimpleDialog () {
          this.openSimple = false;
        },
        update(e){
          let file = e.target.files[0];
          let param = new window.FormData(); //创建form对象
          param.append('file',file);//通过append向form对象添加数据
          // console.log(param.get('file')); //FormData私有类对象，访问不到，可以通过get判断值是否传进去
          this.$ajax.post('/check-car/app/check/uploadCarPic',param)
            .then((res)=>{
              console.log(res);
							let userInfo = JSON.parse(localStorage.getItem('USER'));
							let token = userInfo.token;
							console.log(userInfo)
							console.log(token)
							console.log('http://localhost:8080/check-car/app/showCarPic?token='+token)
							this.headpic= ''
							this.headpic = 'http://localhost:8080/check-car/app/showCarPic?token='+token
//               if (res.data.code == 200){
//                 this.$ajax.get('/check-car/app/showCarPic')
//                   .then((res)=>{
//                     console.log(res);
// 										this.headpic = 'http://localhost:8080/check-car/app/showCarPic'
//                     if (res.data.code == 200){
// 											this.headpic = 'http://localhost:8080/check-car/app/showCarPic'
//                     }
//                   })
//               }
            })
        },
      }
    }
</script>

<style scoped>
.font_pic{
  margin-bottom: 2rem;
  height: 10rem;
  background-color: #ffa2c4;
}
.font_pic img{
  width: 100%;
  height: 100%;
}
.nextBtn{
  margin-left: 15px;
  width: 90%;
}
.suporse{
  margin: 0.5rem auto;
  text-align: center;
}
/*定义上传*/
.fileInput{
  width: 98%;
  height: 160px;
  position: absolute;
  right: 0;
  top: 72px;
  opacity: 0;
}
</style>
