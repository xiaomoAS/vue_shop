<template>
  <div class="login_container">
    <div class="login_box">
        <div class="avatar_box"><img src="../assets/logo.png"></div>
        <el-form ref="loginFormRef" :model="loginForm" :rules="loginFormRules" label-width="0px" class="login_form">
            <el-form-item prop="username">
                <el-input prefix-icon="el-icon-user-solid" v-model="loginForm.username"></el-input>
            </el-form-item>
            <el-form-item prop="password">
                <el-input type="password" prefix-icon="el-icon-s-goods" v-model="loginForm.password"></el-input>
            </el-form-item>
            <el-form-item class="btns">
                <el-button type="primary">登录</el-button>
                <el-button type="info" @click="resetLoginForm">重置</el-button>
            </el-form-item>
        </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      loginForm: {
        username: '',
        password: ''
      },
      loginFormRules: {
        username: [
          { required: 'true', message: 'not null', trigger: 'blur' },
          { min: 3, max: 5, message: 'length 3-5', trigger: 'blur' }
        ],
        password: [
          { required: 'true', message: 'not null', trigger: 'blur' },
          { min: 3, max: 18, message: 'length 3-18', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    resetLoginForm () {
      this.$refs.loginFormRef.resetFields()
    }
  }
}
</script>

<style scoped>
.login_container {
    background-color: #2b4b6b;
    height: 100%;
}

.login_container .login_box {
    width: 450px;
    height: 300px;
    background-color: white;
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}

.login_container .login_box  .avatar_box {
    width: 130px;
    height: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #eee;
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
}

.login_container .login_box  .avatar_box img {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color: #eee;
}

.login_container .login_box .btns {
    display: flex;
    justify-content: end;
}

.login_container .login_form {
    position: absolute;
    bottom: 0;
    width: 100%;
    padding: 0 20px;
    box-sizing: border-box;
}
</style>
