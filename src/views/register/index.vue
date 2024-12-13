<template>
  <div class="register-container">
    <el-form ref="registerForm" :model="registerForm" :rules="registerRules" class="register-form" auto-complete="on" label-position="left">

      <div class="title-container">
        <h3 class="title">register Form</h3>
      </div>

      <el-form-item prop="username">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="registerForm.username"
          placeholder="请输入用户名"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="registerForm.password"
          :type="passwordType"
          placeholder="请输入密码"
          name="password"
          tabindex="2"
          auto-complete="on"
          @keyup.enter.native="handleregister"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>

      <el-form-item prop="checkPassword">
        <span class="svg-container">
          <svg-icon icon-class="checkpassword" />
        </span>
        <el-input
          ref="checkPassword"
          v-model="registerForm.checkPassword"
          placeholder="请再次输入密码"
          name="checkPassword"
          :type="passwordType"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-row type="flex" class="row-bg" justify="space-around">
        <el-col :span="6">
          <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:30px;" @click="onSubmit">注册</el-button>
        </el-col>
        <el-col :span="6">
          <el-button type="default" style="width:100%;margin-bottom:30px;" @click="onCancel">取消</el-button>
        </el-col>
      </el-row>

      <div class="tips">
        <span style="margin-right:20px;">username: admin</span>
        <span> password: any</span>
      </div>

    </el-form>
  </div>
</template>

<script>
import axios from 'axios'

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
      loading: false,
      passwordType: 'password',
      redirect: undefined,
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
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
      this.$nextTick(() => {
        this.$refs.password.focus()
      })
    },
    async onSubmit() {
      this.$refs.registerForm.validate(async valid => {
        if (valid) {
          try {
            // 向后端提交用户名，检验用户是否存在
            const response = await axios.post('http://localhost:8080/api/user/register', { username: this.registerForm.username, password: this.registerForm.password })
            console.log(response)
            if (response.data.code === 400) {
              this.$message({
                message: '用户名已存在',
                type: 'error'
              })
            } else {
              // 用户名不存在，继续注册流程
              this.$message({
                message: '注册成功',
                type: 'success'
              })
              this.dialogVisible = false
              this.$router.push('/login') // 重定向到登录页面
            }
          } catch (error) {
            this.$message({
              message: '服务器错误，请稍后再试',
              type: 'error'
            })
          }
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

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg:#283443;
$light_gray:#fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .register-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.register-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      appearance: none;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 47px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }
}
</style>

<style lang="scss" scoped>
$bg:#2d3a4b;
$dark_gray:#889aa4;
$light_gray:#eee;

.register-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;

  .register-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 160px 35px 0;
    margin: 0 auto;
    overflow: hidden;
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $light_gray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }

  .longer-button {
    width: 200%;
  }
}
</style>
