<template>
  <div class="container">
    <div class="tableList">
      <el-button
        type="primary"
        plain
        style="width: 100px; height: 35px; font-size: 10px; text-align: center"
        @click="toAddDuty(deptId)"
        >+添加值班</el-button
      >
      <el-table :data="tableData" style="width: 100%">
        <el-table-column :label="'值班信息   '" align="center">
          <el-table-column
            prop="realName"
            label="值班医生"
            width="100"
            align="center"
          >
          </el-table-column>

          <el-table-column
            prop="workTime"
            label="日期"
            width="220"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="mobile"
            label="联系方式"
            width="150"
            align="center"
          >
          </el-table-column>

          <el-table-column label="操作" width="100" align="center">
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="danger"
                @click="deleteThisRow(scope.row)"
                >删除</el-button
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
          :total="dutyWorkData.total"
          >dept
        </el-pagination>
      </div>
    </div>
  </div>
</template>
  
  <script>
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      tableData: [],
      page: 1,
      pageSize: 4,
      realName: "",
      titleName: "",
    };
  },
  mounted() {
    this.pageDutyWork();
  },
  computed: {
    ...mapGetters(["dutyWorkData"]),
    records() {
      return this.dutyWorkData.records;
    },
    deptId() {
      return this.$route.params.deptId;
    },
  },

  methods: {
    // 翻页时分页查询
    async handleCurrentChange(val) {
      try {
        const pageSize = this.pageSize;
        await this.$store.dispatch("pageWorkByDept", {
          deptId: this.deptId,
          page: val,
          pageSize,
          realName: this.realName,
        });
        this.tableData = this.records;
      } catch (error) {}
    },

    // 查询数据分页显示
    async pageDutyWork() {
      try {
        await this.$store.dispatch("pageWorkByDept", {
          deptId: this.deptId,
          page: this.page,
          pageSize: this.pageSize,
          realName: this.realName,
        });
        this.tableData = this.records;
      } catch (error) {}
    },

    // 去添加数据
    async toAddDuty(deptId) {
      try {
        await this.$router.push({
          name: "addDuty",
          params: { deptId: deptId },
        });
      } catch (error) {}
    },

    // 删除这条数据
    async deleteThisRow(row) {
      try {
        const res = await this.$store.dispatch("deleteWork", row.workId);
        if (res == "ok") {
          if ((this.dutyWorkData.total - 1) % this.pageSize == 0) {
            await this.$store.dispatch("pageWorkByDept", {
              deptId: this.deptId,
              page: this.page - 1,
              pageSize: this.pageSize,
              realName: this.realName,
            });
            this.tableData = this.records;
          }else{
            this.pageDutyWork()
          }
          this.$message({
            message: "删除成功",
            type: "success",
            showClose: true,
          });
        }
      } catch (error) {}
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