<template>
  <div class="scrollbar blockLayout" v-loading="loading">
    <el-form style="width: 50%"
      :model="ruleForm"
      status-icon
      :rules="rules"
      ref="ruleForm"
      label-width="100px"
      @submit.native.prevent="submitForm"
    >
      <el-form-item label="原密码" prop="password">
        <el-input v-model="ruleForm.password"></el-input>
      </el-form-item>
      <el-form-item label="新密码" prop="newPassword">
        <el-input type="password" v-model="ruleForm.newPassword" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="checkPass">
        <el-input type="password" v-model="ruleForm.checkPass" autocomplete="off"></el-input>
      </el-form-item>
      
      <el-form-item>
        <el-button type="primary" native-type="submit">提交</el-button>
        <el-button @click="resetForm">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import * as user from '@/common/api/user';

export default {
  data() {
    
    const validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else {
        if (this.ruleForm.checkPass !== "") {
          this.$refs.ruleForm.validateField("checkPass");
        }
        callback();
      }
    };
    const validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleForm.newPassword) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };

    return {
      loading: false,
      ruleForm: {
        password: "",
        checkPass: "",
        newPassword: ""
      },
      rules: {
        newPassword: [
          { validator: validatePass, trigger: "blur" },
          { min: 6, max: 30, message: '长度在 6 到 30 个字符', trigger: 'blur' }
        ],
        checkPass: [{ validator: validatePass2, trigger: "blur" }],
        password: [
          { required: true, message: '请输入原密码', trigger: 'blur' }
        ]
      }
    };
  },
  methods: {
    submitForm() {
      this.$refs.ruleForm.validate(valid => {
        if (valid) {
          this.submit()
        } 
      });
    },
    submit: function(){
      this.loading = true;
      let queryParam = Object.assign({}, this.ruleForm);
      delete queryParam.checkPass;
      user.editPassword(queryParam).then((res) => {
        this.loading = false;
        if(res.data.code===200){
          this.resetForm();
          this.$message({
            message: '操作成功',
            type: 'success'
          });
        }else{
          this.$message({
            message: res.data.msg,
            type: 'warning'
          });
        }
        
      })
    },
    resetForm() {
      this.$refs.ruleForm.resetFields();
    }
  },
  created: function() {}
};
</script>

<style scoped>
</style>
