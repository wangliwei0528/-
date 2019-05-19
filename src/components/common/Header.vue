<template>
  <div class="header">
    <!-- 折叠按钮 -->
    <div class="collapse-btn" @click="collapseChage">
      <i class="el-icon-menu"></i>
    </div>
    <div class="logo">聚合支付第三方平台</div>
    <div class="header-right">
       <!-- 时间展示 -->
      <div class="time">
          {{date | formatDate}}
      </div>
      <div class="header-user-con">        
        <!-- 用户头像 -->
        <div class="user-avator">
          <img src="static/img/img.jpg">
        </div>
        <!-- 用户名下拉菜单 -->
        <el-dropdown class="user-name" trigger="click" @command="handleCommand">
          <span class="el-dropdown-link">
            您好：{{username}}
            <i class="el-icon-caret-bottom"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item divided command="editPassword">修改密码</el-dropdown-item>
            <el-dropdown-item divided command="loginout">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown> 
           <!-- 全屏显示 -->
        <div class="btn-fullscreen" @click="handleFullScreen">
          <el-tooltip effect="dark" :content="fullscreen?`取消全屏`:`全屏`" placement="bottom">
            <i class="el-icon-rank"></i>
          </el-tooltip>
        </div>
        <!-- 编辑弹出框 -->
        <el-dialog title="修改密码" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" :rules="rules" label-width="100px">
                <el-form-item label="原密码" prop='oldPassword'>
                     <el-input type='password' v-model="form.oldPassword"></el-input>
                </el-form-item>
                <el-form-item label="新密码" prop='newPassword'>
                    <el-input type='password' v-model="form.newPassword"></el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop='rePassword'>
                    <el-input type='password' v-model="form.rePassword"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit('form')">确 定</el-button>
            </span>
        </el-dialog>       
      </div>
    </div>
  </div>
</template>
<script>
import bus from "../common/bus";
import moment from 'moment'
import 'moment/locale/zh-cn'
export default {
  data() {
    return {
      collapse: false,
      fullscreen: false,
      date: new Date(),
      editVisible: false,
      form:{
        oldPassword:'',
        newPassword:'', 
        rePassword:''      
      },
      rePassword:'',
      rules: {
        oldPassword: [{ required: true, message: "请输入原密码", trigger: "blur" }],
        newPassword: [{ required: true, message: "请输入新密码", trigger: "blur" }],
        rePassword: [{ required: true, message: "请确认密码", trigger: "blur" }]
      },
    };
  },
  filters: {
      formatDate: function(value) {
        return moment(value).format("YYYY年MM月DD日-dddd HH:mm:ss");
      }
    },  
  computed: {
    
    username() {
      let username = localStorage.getItem("name");
      return username;
    },
    userType() {
      let userType = localStorage.getItem("userType");
      return userType;
    }
  },
  methods: {
    loginout(){
      this.$axios
          .post("/juhepay/login/loginOut")
          .then(res => {
            if (res.data.success == true) {
              this.$message({
                message: res.data.msg,
                type: "success"
              });
              localStorage.removeItem("name");
              localStorage.removeItem("userType");
              this.$router.push("/login");
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
    },
    // 用户名下拉菜单选择事件
    handleCommand(command) {
      if (command == "loginout") {
        this.loginout();
      }
      if (command == "editPassword") {
        this.editVisible = true
      }
    },
    saveEdit(form){
      let data = {
        oldPassword : this.form.oldPassword,
        newPassword : this.form.newPassword
      }
      this.$refs[form].validate(valid => {
        if (valid) {
           if(this.form.newPassword === this.form.rePassword){
             this.$axios.post('/juhepay/users/updatePassword',data).then(
              res=>{
                if(res.data.success == true){
                   this.$message({
                    message: res.data.msg,
                    type: "success"
                  });
                  this.editVisible = false;
                  setTimeout(()=>{this.loginout();},1000)               
                }else{
                  this.$message({
                    message: res.data.msg,
                    type: "error"
                  });
                }
              }
             ).catch(err=>{console.log(err)})
           }else{
             this.$message.error('两次密码不一致，请重新输入')
           }
        }
      })
    },
    // 侧边栏折叠
    collapseChage() {
      this.collapse = !this.collapse;
      bus.$emit("collapse", this.collapse);
    },
    // 全屏事件
    handleFullScreen() {
      let element = document.documentElement;
      if (this.fullscreen) {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        }
      } else {
        if (element.requestFullscreen) {
          element.requestFullscreen();
        } else if (element.webkitRequestFullScreen) {
          element.webkitRequestFullScreen();
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if (element.msRequestFullscreen) {
          // IE11
          element.msRequestFullscreen();
        }
      }
      this.fullscreen = !this.fullscreen;
    }
  },
  mounted() {
    if (document.body.clientWidth < 1500) {
      this.collapseChage();
    };
    let _this = this;
    this.timer = setInterval(() => {
      _this.date = new Date();
    }, 1000)
  },
  beforeDestroy() {
    if (this.timer) {
      clearInterval(this.timer); // 在Vue实例销毁前，清除我们的定时器
    }
  },
};
</script>
<style scoped>
.time{
  font-size:14px;
  line-height: 30px;
}
.header {
  position: relative;
  box-sizing: border-box;
  width: 100%;
  height: 70px;
  font-size: 22px;
  color: #fff;
}
.collapse-btn {
  float: left;
  padding: 0 21px;
  cursor: pointer;
  line-height: 70px;
}
.header .logo {
  float: left;
  width: 250px;
  line-height: 70px;
}
.header-right {
  float: right;
  padding-right:20px;
}
.header-user-con {
  display: flex;
  height: 30px;
  align-items: center;
}
.btn-fullscreen {
  transform: rotate(45deg);
  margin-right: 5px;
  font-size: 24px;
}
.btn-bell,
.btn-fullscreen {
  position: relative;
  width: 30px;
  height: 30px;
  text-align: center;
  border-radius: 15px;
  cursor: pointer;
  margin-left:22px;
  margin-top:7px;
}
.btn-bell-badge {
  position: absolute;
  right: 0;
  top: -2px;
  width: 8px;
  height: 8px;
  border-radius: 4px;
  background: #f56c6c;
  color: #fff;
}
.btn-bell .el-icon-bell {
  color: #fff;
}
.user-name {
  margin-left: 10px;
}

.user-avator img {
  display: block;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  margin-right:10px;
}
.el-dropdown-link {
  color: #fff;
  cursor: pointer;
}
.el-dropdown-menu__item {
  text-align: center;
}
</style>
