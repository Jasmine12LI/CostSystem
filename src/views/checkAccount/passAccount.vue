<template>
  <div class="app-container box">
    <div v-if="checkPermission(['fundManager'])">
    <div class="filter-container">
      <el-input
        placeholder="请输入编号"
        prefix-icon="el-icon-search"
        v-model="search"
        style="width: 200px"
      ></el-input>
    </div>
    <!-- <div v-if="!isLoading"> -->
    <el-table
      :data="perData.filter(data => !search || data.accountId==search)"
      stripe
      border
      style="width:100%;margin-top: 20px"
    >
    <el-table-column type="expand">
        <template slot-scope="props">
          <el-table :data="props.row.items" style="width: 100%">
            <el-table-column prop="name" label="报销项" width="180"></el-table-column>
            <el-table-column prop="itemPrice" label="金额" width="180"></el-table-column>
            <el-table-column prop="des" label="备注"></el-table-column>
          </el-table>
          <div>
            <p>摘要：{{props.row.summary}}</p>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="accountId" label="编号" width="150"></el-table-column>
      <el-table-column prop="accountDevice.deviceName" label="设备名称" width="150"></el-table-column>
      <el-table-column prop="submitUser.userName" label="提交人" width="150"></el-table-column>
      <el-table-column  label="提交时间" width="150">
          <template slot-scope="{row}">
             <span>{{ row.submitTime}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="checkUser.userName" label="审核人" width="150"></el-table-column>
      <el-table-column  label="审核时间" width="150">
          <template slot-scope="{row}">
             <span>{{ row.checkTime}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="des" label="审核意见" width="150"></el-table-column>
    </el-table>

    <div v-loading="isLoading" style="margin-top:200px"></div>
    <el-pagination
      background
      class="page"
      layout="prev, pager, next"
      :current-page.sync="currentPage"
      @current-change="handleCurrentChange"
      :page-size="pageSize"
      :pager-count="5"
      :total="totalNum"
    ></el-pagination>
    </div>
    <div v-else>
      <h3>您没有权限访问！</h3>
    </div>
  </div>
</template>

<script>
import { findAccountByStatus, passAccount, noPassAccount ,printPdf} from "@/api/account";
import waves from "@/directive/waves"; // waves directive
import { parseTime } from "@/utils";
import Pagination from "@/components/Pagination";
import checkPermission from '@/utils/permission';

export default {
  name: "ComplexTable",
  components: { Pagination },
  directives: { waves },
  data() {
    return {
      typeValue: "",
      activeIndex: "1",
      isLoading: true,
      isAdd: false,
      totalNum: 0,
      pageSize: 5,
      search:'',
      currentPage: 1,
      formLabelWidth: "100px",
      info: {
        name: "",
        des: "",
      },
      tableData: [
        {
          name: "",
          des: "",
        }
      ],
      perData: [],
    };
  },
  methods: {
    handleCurrentChange() {
      this.getCurrentData();
    },
    getCurrentData() {
      var x = this.currentPage * this.pageSize;
      var x0 = (this.currentPage - 1) * this.pageSize;
      if (x <= this.totalNum) this.perData = this.tableData.slice(x0, x);
      else this.perData = this.tableData.slice(x0);
    },
    checkPermission,
    message(code) {
      if (code == 200)
        this.$message({
          message: "操作成功！",
          type: "success"
        });
      else {
        this.$message.error("操作失败！");
      }
    }
  },
  mounted() {
    var that = this;
    findAccountByStatus(1).then(res => {
      console.log("passaccount");
      console.log(res.data);
      that.tableData = res.data;
      that.isLoading = false;
      that.totalNum = res.data.length;
      that.info = that.tableData[0];
      that.getCurrentData();
    });
  }
};
</script>
<style scoped>
.page {
  position: fixed;
  bottom: 30px;
  left: 50%;
}
</style>

