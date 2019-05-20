<template>
  <div class="login-wrap">
    <div class="ms-login">
      <div class="ms-title">聚合支付第三方平台</div>
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="0px" class="ms-content">
        <el-form-item prop="username">
          <el-input v-model.trim="ruleForm.username" placeholder="用户名">
            <el-button slot="prepend" icon="el-icon-lx-people"></el-button>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input type="password" placeholder="密码" v-model.trim="ruleForm.password" >
            <el-button slot="prepend" icon="el-icon-lx-lock"></el-button>
          </el-input>
        </el-form-item>
         <el-form-item prop="code">
            <el-input type="text" v-model.trim="ruleForm.code" class='fl' @keyup.enter.native="submitForm('ruleForm')">
                <el-button slot="prepend">验证码</el-button>              
            </el-input>
             <div class="code" @click="refreshCode">
                <SIdentify :identifyCode="identifyCode"></SIdentify>
             </div>              
        </el-form-item>
        <div class="login-btn">
          <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
import SIdentify from '@/components/common/code'
export default {
  components:{SIdentify},
  data: function() {
    return {
      identifyCodes: "1234567890",
      identifyCode: "",
      ruleForm: {
        username: "",
        password: "",
        code:""
      },
      rules: {
        username: [{ required: true, message: "请输入用户名", trigger: "blur" }],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        code: [{ required: true, message: "请输入验证码", trigger: "blur" }]
      },
    };
  },
   mounted() {
    this.identifyCode = "";
    this.makeCode(this.identifyCodes, 4);
  },
  methods: { 
      randomNum(min, max) {
      return Math.floor(Math.random() * (max - min) + min);
    },
    refreshCode() {
      this.identifyCode = "";
      this.makeCode(this.identifyCodes, 4);
    },
    makeCode(o, l) {
      for (let i = 0; i < l; i++) {
        this.identifyCode += this.identifyCodes[
          this.randomNum(0, this.identifyCodes.length)
        ];
      }
    },    
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
            if(this.ruleForm.code === this.identifyCode){
              this.$axios.post('/juhepay/login/login',this[formName]).then(res=>{
                if (res.data.success == true) {
                  this.$message({
                    message: res.data.msg,
                    type: "success"
                  });
                  let {name,userType} = res.data.data;
                  this.$router.push("/user");
                  localStorage.setItem("name", name);
                  localStorage.setItem("userType", userType);         
                } else {
                  this.$message({
                    message: res.data.msg,
                    type: "error"
                  });
                }
                 
              }).catch(err=>{console.log(err)})               
            }
            else{
                this.$message.error('验证码错误');
            }        
        }
        else {
          return false;
        } 
      });
    }
  }
};
</script>

<style scoped>
.fl{
    float:left;
    width:66%
}
.code {
  width: 30%;
  height: 40px;
  float:right;
}
.login-wrap {
  position: relative;
  width: 100%;
  height: 100%;
  background-image: url(../../assets/login-bg.jpg);
  background-size: 100%;
}
.ms-title {
  width: 100%;
  line-height: 50px;
  text-align: center;
  font-size: 20px;
  color: #fff;
  border-bottom: 1px solid #ddd;
}
.ms-login {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 350px;
  margin: -190px 0 0 -175px;
  border-radius: 5px;
  background: rgba(255, 255, 255, 0.3);
  overflow: hidden;
}
.ms-content {
  padding: 30px 30px;
}
.login-btn {
  text-align: center;
}
.login-btn button {
  width: 100%;
  height: 36px;
  margin-bottom: 10px;
}
.login-tips {
  font-size: 12px;
  line-height: 30px;
  color: #fff;
}
</style>