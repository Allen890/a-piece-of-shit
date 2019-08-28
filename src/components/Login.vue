<template>
  <div class="login">
    <el-form ref="form" :model="form" :rules="rules" status-icon label-width="80px">
      <img src="../assets/7777.jpg" alt />
      <el-form-item label="用户名" prop="username">
        <el-input  @keyup.enter.native='login' v-model="form.username" placeholder="请输入用户名"></el-input>
      </el-form-item>
      <el-form-item label="密 码" prop="password">
        <el-input  @keyup.enter.native='login' type="password" v-model="form.password" placeholder="请输入密码"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" class="loginBtn" @click="login">登录</el-button>
        <el-button @click="fn">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      form: {
        username: '',
        password: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 3,
            max: 5,
            message: '长度在 3 到 5 个字符',
            trigger: ['change', 'blur']
          }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 3,
            max: 12,
            message: '长度在 3 到 12 个字符',
            trigger: ['change', 'blur']
          }
        ]
      }
    }
  },
  methods: {
    fn () {
      this.$refs.form.resetFields()
    },
    login () {
      console.log(123)
      this.$refs.form.validate(isValid => {
        if (!isValid) return
        axios({
          method: 'post',
          url: 'http://localhost:8888/api/private/v1/login',
          data: this.form
        }).then(res => {
          const { meta, data } = res.data
          localStorage.setItem('token', data.token)
          if (meta.status === 200) {
            this.$message({
              message: meta.msg,
              type: 'success'
            })

            this.$router.push('/index')
          } else {
            this.$message.error(meta.msg)
          }
        })
      })
    }
  }
}
</script>

<style lang='scss'>
.login {
  width: 100%;
  height: 100%;
  background-color: #2d434c;
  overflow: hidden;
  .el-form {
    width: 440px;
    padding: 20px;
    padding-top: 70px;
    background-color: #fff;
    border-radius: 20px;
    margin: 200px auto;
    position: relative;
    .loginBtn {
      margin-right: 70px;
    }
    img {
      width: 130px;
      height: 130px;
      border-radius: 50%;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      top: -70px;
      border: 5px solid #fff;
    }
  }
}
</style>
