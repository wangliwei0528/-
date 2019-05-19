<template>
  <div class="wrap">
      <div slot="header" class="clearfix">
       <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="80px" class="ms-content" >
        <el-form-item label="用户名">
            <el-input v-model="ruleForm.username" :disabled="true"></el-input>
        </el-form-item>
          <el-form-item label="AppSecret">
            <el-input v-model="ruleForm.appSecret" :disabled="true"></el-input>
        </el-form-item> 
        <el-form-item label="开户行" prop='openBank'>
            <el-input v-model="ruleForm.openBank" placeholder="银行"></el-input>
        </el-form-item>  
        <el-form-item label="用户类型" prop='userType'>
             <el-select v-model="ruleForm.userType" placeholder="请选择用户类型">
                <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
                </el-option>
            </el-select>
        </el-form-item> 
         <el-form-item label="接入费率" prop='accessRate'>
            <el-input v-model="ruleForm.accessRate" placeholder="接入费率" type='number' :min=0></el-input>
        </el-form-item>   
        <el-form-item label="用户备注" prop='userRemark'>
            <el-input v-model="ruleForm.userRemark" placeholder="用户备注"></el-input>
        </el-form-item>
         <el-form-item label="登录密码" prop='password'>
            <el-input v-model="ruleForm.password" placeholder="登录密码"></el-input>
        </el-form-item>       
         <el-form-item label="下发密码" prop='payPassword'>
            <el-input v-model="ruleForm.payPassword" placeholder="下发密码"></el-input>
        </el-form-item>        
        <div class="login-btn">
          <el-button type="primary" @click="submitForm('ruleForm')">确定</el-button>
        </div>
      </el-form>
      </div>
  </div>
</template>

<script>
import SIdentify from "@/components/common/code";
export default {
  components: { SIdentify },
  data: function() {
    return {
       options: [],
       ruleForm:{
           username:'',//用户名
           password:'',
           userType:'',
           payPassword:'',
           appSecret:'',
           openBank:'',
           accessRate:'',
           userRemark:''
       },
        rules: {
            password: [{ required: true, message: "请输入密码", trigger: "blur" }],
            userType: [{ required: true, message: "请选择用户类型", trigger: "blur" }],
            payPassword: [{ required: true, message: "请输入支付密码", trigger: "blur" }],
            openBank: [{ required: true, message: "请输入开户行", trigger: "blur" }],
            accessRate: [{ required: true, message: "请输入接入费率", trigger: "blur" }],
            userRemark: [{ required: true, message: "请输入用户备注", trigger: "blur" }]
      },
      
    };
  },
  created(){
    //  用户类型
    let userType = localStorage.getItem("userType");
    if(userType == 0){//数组存在代理1  用户
        this.options = [
            { value: '1',label: '代理'},
            { value: '3',label: '用户'},
        ]
    }
    if(userType == 1){
         this.options = [
            { value: '2',label: '代理'},
            { value: '3',label: '用户'},
        ]
    }
    if(userType == 2){
        this.options = [
            { value: '3',label: '用户'},
        ]
    }
      //   获取登录名和秘钥
    this.getUsername()

  },
  methods: {
    //   获取登录名和秘钥
    getUsername(){
         this.$loading.show()
       this.$axios.post('/juhepay/users/getUsernameAndAppsecret').then(res=>{
            this.$loading.hide()
           if(res.data.success == true){
               this.ruleForm.username = res.data.data.username;
               this.ruleForm.appSecret = res.data.data.appSecret;
           }
       }).catch(err=>{
           console.log(err)
       })
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
             this.$loading.show()
            this.$axios
              .post("/juhepay/users/createUsers", this[formName])
              .then(res => {
                   this.$loading.hide()
                if (res.data.success == true) {
                  this.$message({
                    message: res.data.msg,
                    type: "success"
                  });
                  if(this.ruleForm.userType == 1 || this.ruleForm.userType ==2){
                        this.$router.push('/agent')
                  }
                  if(this.ruleForm.userType == 3){
                        this.$router.push('/user')
                  }
                } else {
                  this.$message({
                    message: res.data.msg,
                    type: "error"
                  });
                }
              })
              .catch(err => {
                console.log(err);
              });
        } else {
          return false;
        }
      });
    }
  }
};
</script>

<style scoped>
.wrap{
    background:#fff;
    padding:30px;
    height:100%
}
.ms-content{
    width:35%;
    margin:60px auto;
}
.login-btn {
  width: 100%;
  height: 36px;
  position: relative;;
}
button {
width: 50%;
height: 36px;
margin-left:25%;
margin-top:50px;
}
</style>