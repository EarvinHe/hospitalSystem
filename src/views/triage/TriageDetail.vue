<template>
  <div class="container">
    <div class="tableList">
      <el-table :data="tableData" style="width: 100%">
        <el-table-column :label="'科室患者信息   ' + deptName" align="center">
         
          <el-table-column type="index" :index="indexMethod"  width="60" align="center" label="序列">
        </el-table-column>

          <el-table-column
          v-if="false"
            prop="patientId"
            label="编号"
            width="120"
            align="center"
          >
          </el-table-column>

          <el-table-column
            prop="patientName"
            label="姓名"
            width="120"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="patientSex"
            label="性别"
            width="120"
            align="center"
          >
          <template slot-scope="scope">
              {{ getGenderText(scope.row.patientSex) }}
            </template>
          </el-table-column>
          <el-table-column
            prop="patientBirthday"
            label="出生年月"
            width="250"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="patientRegisterStatus"
            label="状态"
            width="120"
            align="center"
          >
            <!-- 设置插槽来将数字转成对应的中文意义 -->
            <template slot-scope="scope">
              {{ getSatusText(scope.row.patientRegisterStatus) }}
            </template>
          </el-table-column>
          <el-table-column label="操作" width="220" align="center">
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="success"
                @click="callPatient(scope.row)"
                >叫号</el-button
              >

              <el-button
                size="mini"
                type="primary"
                @click="toDiagnosis(scope.row)"
                >诊断</el-button
              >
            </template>
          </el-table-column>
        </el-table-column>
      </el-table>
      <div class="pagination">
        <!-- <span class="demonstration">直接前往</span> -->
        <el-pagination
          @current-change="handleCurrentChange"
          :current-page.sync="page"
          :page-size="pageSize"
          layout="prev, pager, next, jumper"
          :total="patientDeptData.total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapState } from "vuex";
export default {
  data() {
    return {
      tableData: [
        {
          patientId: "",
          patientName: "",
          patientSex: "",
          patientBirthday: "",
          patientRegisterStatus: "",
        },
      ],
      page: 1,
      pageSize: 4,
      titleName: "",
    };
  },
  mounted() {
    this.queryPatientByDeptId();
  },
  computed: {
    ...mapGetters(["patientDeptData", "userId"]),
    records() {
      return this.patientDeptData.records;
    },
    deptId() {
      return this.$route.params.deptId;
    },
    deptName() {
      return this.$route.query.deptName;
    },
    ...mapState({
      userId: (state) => state.user.userData.userId,
      realName: (state) => state.user.userData.realName,
    }),
  },

  methods: {
     // 自定义索引
     indexMethod(index) {
      return (this.page-1)*this.pageSize + (index + 1);
    },
    // 将代表性别的数字转换程文字
    getGenderText(sex) {
      return sex === 0 ? "女" : "男";
    },
    // 状态栏转义
    getSatusText(patientRegisterStatus) {
      if (patientRegisterStatus == 1) {
        return "诊断中";
      } else if (patientRegisterStatus == 0) {
        return "待诊";
      } else {
        return "诊断结束";
      }
    },

    // 翻页时分页查询
    async handleCurrentChange(val) {
      try {
        const pageSize = this.pageSize;
        await this.$store.dispatch("queryPatientByDeptId", {
          deptId: this.deptId,
          page: val,
          pageSize,
        });
        this.tableData = this.records;
      } catch (error) {}
    },

    // 查询数据分页显示
    async queryPatientByDeptId() {
      try {
        const page = this.page;
        const pageSize = this.pageSize;
        await this.$store.dispatch("queryPatientByDeptId", {
          deptId: this.deptId,
          page,
          pageSize,
        });
        this.tableData = this.records;
      } catch (error) {}
    },

    // 叫号操作，修改诊断状态
    async callPatient(row) {
      try {
        const status = 1;
        const id = row.id;
        const res = await this.$store.dispatch("updatePatientStatus", { status, id });
        if(res == 'ok'){
          this.$message({
          message: "叫号成功",
          type: "success",
          showClose: true,
        });
        }
        
        this.queryPatientByDeptId();
      } catch (error) {}
    },

    // 诊断结果
    toDiagnosis(row) {
      console.log(row);
      //  const data = {
      const id = row.id;
      const patientId = row.patientId;
      const patientName = row.patientName;
      const deptId = row.deptId;
      console.log(deptId)
      const deptName = this.deptName;
      const userId = this.userId;
      const realName = this.realName;
      // }
      // const userid = JSON.parse(JSON.stringify(this.userId))
      this.$router.push({
        path: "/DiagnosisResult",
        query: { id: id, patientId: patientId, patientName: patientName, 
      deptId:deptId,deptName:deptName,userId:userId,realName:realName}});
    },
  },
};
</script>

<style lang="less" scoped>
.container {
  width: 100%;
  height: 100%;
  flex-direction: column;
  align-items: center;
  position: fixed;
  .tableList {
    height: 100%;
    position: absolute;
    // width: 100%;
    left: 50%;
    transform: translate(-50%, 20%);
  }
  .pagination {
    position: absolute;
    left: 50%;
    margin-top: 15px;
    transform: translateX(-50%);
  }
}
</style>