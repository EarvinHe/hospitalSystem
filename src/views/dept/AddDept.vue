<template>
  <div class="addPage">
    <div class="container">
      <h1 class="addTitle">科室添加和修改</h1>
      <el-form ref="addDeptForm" :model="addDeptForm" label-width="100px">
        <el-form-item label="科室名称">
          <el-input v-model="addDeptForm.deptName"></el-input>
        </el-form-item>
        <el-form-item label="办公室电话">
          <el-input v-model="addDeptForm.telephone"></el-input>
        </el-form-item>
        <el-form-item label="所在区域">
          <el-input v-model="addDeptForm.location"></el-input>
        </el-form-item>
        <el-button type="success" plain @click="addDept">添加</el-button>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: "addDept",
  data() {
    return {
      addDeptForm: {
        deptName: "",
        telephone: "",
        location: "",
      },
    };
  },
  methods: {
    async addDept() {
      try {
      const res =  await this.$store.dispatch("addDept", this.addDeptForm);
        if (res == "ok") {
          this.$message({
            message: "添加成功",
            type: "success",
            showClose: true,
          });
        }
        // 跳转科室管理
        this.$router.push("/dept");
      } catch (error) {}
    },
  },
};
</script>

<style lang="less" scoped >
.addPage {
  width: 100%;
  height: 100%;
  display: flex;
  background-size: cover;
  background-image: url(../../assets/h2.jpg);
  overflow: hidden; /* 禁止滚动条 */
  background-repeat: no-repeat;
  justify-content: center;
  align-items: center;
  position: relative;
  .container {
    box-shadow: 3px 3px 10px 3px rgba(199, 196, 196, 0.5);
    background-color: rgba(255, 255, 255, 0.5);
    height: 300px;
    border: 1px solid rgb(239, 239, 239);
    border-radius: 6px;
    left: 50%;
    // top: 50%;
    margin: 0;
    position: absolute;
    transform: translate(-50%, -50%);
    .el-form {
      margin: 20px;
    }
    .el-form-item {
      width: 700px;
    }
    .el-button {
      left: 50%;
      // top: 50%;
      margin: 0;
      position: absolute;
      transform: translateX(-50%);
      text-align: center;
      width: 150px;
    }

    .addTitle {
      font-size: larger;
      text-align: center;
      margin: 10px 5px 20px 5px;
    }
  }
}
</style>