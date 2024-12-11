<template>
  <div class="register-container">
    <el-dialog
      :visible.sync="dialogVisible"
      width="30%"
      title="注册"
    >
      <el-form ref="registerFormRef" :model="registerForm" :rules="registerRules" label-width="80px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="registerForm.username" />
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="registerForm.password" type="password" show-password />
        </el-form-item>
        <el-form-item label="确认密码" prop="checkPassword">
          <el-input v-model="registerForm.checkPassword" type="password" show-password />
        </el-form-item>
        <el-form-item>
          <div class="button-container">
            <el-button type="primary" @click="onSubmit">注册</el-button>
            <el-button @click="onCancel">取消</el-button>
          </div>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error('密码不能少于6位'))
      } else {
        callback()
      }
    }
    const validateCheckPassword = (rule, value, callback) => {
      if (value !== this.registerForm.password) {
        callback(new Error('两次输入的密码不一致'))
      } else {
        callback()
      }
    }
    return {
      dialogVisible: true,
      registerForm: {
        username: '',
        password: '',
        checkPassword: ''
      },
      registerRules: {
        username: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
        password: [{ validator: validatePassword, trigger: 'blur' }],
        checkPassword: [{ validator: validateCheckPassword, trigger: 'blur' }]
      }
    }
  },
  methods: {
    onSubmit() {
      this.$refs.registerFormRef.validate(valid => {
        if (valid) {
          // 处理注册逻辑
          this.$message({
            message: '注册成功',
            type: 'success'
          })
          this.dialogVisible = false
          this.$router.push('/login') // 重定向到登录页面
        } else {
          this.$message({
            message: '请填写正确的信息',
            type: 'error'
          })
          return false
        }
      })
    },
    onCancel() {
      this.dialogVisible = false
      this.$router.push('/login') // 重定向到登录页面
    }
  }
}
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: url('../../assets/background2.jpg') center;
  background-size: cover;
}

.button-container {
  display: flex;
  justify-content: center;
  gap: 10px; /* 按钮之间的间隔 */
}
</style>
