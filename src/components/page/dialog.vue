<template>
  <el-dialog title="查看详情" :visible.sync="viewVisible" width="30%">
    <el-form ref="form" :model="detail" label-width="100px">
      <el-form-item label="用户名">
        <el-input type="text" v-model="detail.username" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="用户类型">
        <el-input type="text" :value="userType" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="用户状态">
        <el-input type="text" v-model="userState" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="所属代理">
        <el-input type="text" v-model="detail.agentname" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="开户银行">
        <el-input type="text" v-model="detail.openBank" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="费率">
        <el-input type="text" v-model="detail.accessRate" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="AppSecret">
        <el-input type="text" v-model="detail.appSecret" :disabled="true"></el-input>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>
<script>
export default {
  props: ["message"],
  data() {
    return {
      viewVisible: false,
      detail: {}
    };
  },
  watch: {
    message: function(newVlue, oldvalue) {
      this.message = newVlue;
      this.getData();
    }
  },
  computed: {
    userType() {
      if (this.detail.userType == 3) {
        return "用户";
      }
      if (this.detail.userType == 2) {
        return "二级代理";
      }
      if (this.detail.userType == 1) {
        return "一级代理";
      }
      if (this.detail.userType == 0) {
        return "总平台";
      }
    },
    userState() {
      if (this.detail.userState == 0) {
        return "正常";
      }
      if (this.detail.userState == 1) {
        return "禁用";
      }
    }
  },
  methods: {
    getData() {
      this.viewVisible = true;
      this.$axios.post("/juhepay/users/getUsersInfo", { id: this.message }).then(res => {
          if (res.data.success == true) {
            this.detail = res.data.data;
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

