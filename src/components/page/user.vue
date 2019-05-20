<template>
  <div class="table">
    <div class="container">
    <div class="handle-box">
        代理账号：
        <el-input v-model="select_word" placeholder="appid" class="handle-input mr10"></el-input>
        <el-button type="primary" icon="search" @click="search">搜索</el-button>
    </div>
      <el-table :data="tableData" border class="table" ref="multipleTable">
        <el-table-column prop="username" label="用户名"></el-table-column>
        <el-table-column prop="appSecret" label="AppSecret" ></el-table-column>
        <el-table-column prop="accessRate" label="费率"></el-table-column>
        <el-table-column prop="userState" label="状态">
            <template slot-scope="scope" >
                <span :style="{color: scope.row.userState == 0?'#4F4F4F':'red'}">
                    {{scope.row.userState == 0 ?'正常':'禁用'}}
                </span>
            </template>
        </el-table-column>
        <el-table-column prop="todayMoney" label="当日收益"></el-table-column>
        <el-table-column prop="agentname" label="所属代理"></el-table-column>
        <el-table-column label="操作"  align="center">
          <template slot-scope="scope">               
              <el-button type="text"  @click="handleEdit(scope.$index, scope.row)">{{scope.row.userState == 0 ?'禁用':'启用'}}</el-button>
              <el-button type="text"  @click="handleView(scope.$index, scope.row)">查看</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="pagination">
        <el-pagination
          @current-change="handleCurrentChanges"
          @size-change="sizeChange"
          :current-page="pageNum"
          :page-sizes="pagesizes"
          :page-size="pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          :background="true"
        ></el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "basetable",
  data() {
    return {
        tableData: [], //列表数据
        pageNum: 1,
        total: 0, //总数默认为0
        currentPage: 1, //当前页默认为1
        pagesize: 10, //每页显示的数据
        pagesizes: [10, 15, 20], //每页显示的数据
        page: 1, //当前页第一页
        per_page: 0, //前一页
        pagination: false, //分页器
        select_word:null,//搜索关键词
    };
  },
  created() {
    this.getData();
  },
  methods: {
   //页码变更显示当前页的数据
      handleCurrentChanges: function(currentPage) {
        this.pageNum = currentPage;
        this.getData();
      },
      //pageSize 改变时会触发
      sizeChange(pagesize) {
        this.pagesize = pagesize;
        this.getData();
      },
    // 获取数据
    getData() {
    //  this.$loading.show()
     let data = {
          pageNum: this.pageNum,
          pageSize: this.pagesize,
          param: {
              username: this.select_word ? this.select_word :null
          }
        };
      this.$axios
        .post("/juhepay/users/getUsersPage",data)
        .then(res => {
        // this.$loading.hide()
         if (res.data.success == true) {
            this.tableData = res.data.data.records;
            this.total = res.data.data.total;
          }
        }).catch(err=>{
          //  this.$loading.hide()
          console.log(err)
          });
    },
    // 查看
    handleView(index,row){},
    // 禁用启用
    handleEdit(index,row){
        let userState = 1;
        if(row.userState == 1){
            userState = 0
        }
        if(row.userState == 0){
            userState = 1
        }
        let data = {
            id : row.id,
            userState : userState 
        }
        //  this.$loading.show()
        this.$axios.post('/juhepay/users/updateUserstate',data).then(res=>{
            //  this.$loading.hide()
            if (res.data.success == true) {
              this.$message({
                message: res.data.msg,
                type: "success"
              });
                this.getData()
            } else {
              this.$message({
                message: res.data.msg,
                type: "error"
              });
              this.getData()
            }
        })
        .catch(err=>{
          //  this.$loading.hide()
            console.log(err)
        })
    },
    // 搜索
    search(){
        this.getData()
    }
  
  }
};
</script>

<style scoped>
.handle-box {
  margin-bottom: 20px;
}

.handle-select {
  width: 120px;
}

.handle-input {
  width: 300px;
  display: inline-block;
}
.del-dialog-cnt {
  font-size: 16px;
  text-align: center;
}
.table {
  width: 100%;
  font-size: 14px;
}


</style>
