<template>
  <div class="container">
    <div class="login-wrapper">
      <div class="header">欢迎登录</div>
      <el-form ref="loginForm" :model="loginForm" :rules="loginRules" auto-complete="on" label-position="left">

        <div class="form-wrapper">
          <el-input ref="email" v-model="loginForm.username" placeholder="Email" name="email" type="text" tabindex="1"
            auto-complete="on" class="input-item" />
          <el-input :key="passwordType" ref="password" v-model="loginForm.password" :type="passwordType"
            placeholder="Password" name="password" tabindex="2" auto-complete="on" @keyup.enter.native="handleLogin"
            class="input-item" />
          <el-button :loading="loading" type="primary" class="btn" @click.native.prevent="handleLogin">登录</el-button>

        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
import { validUsername } from '@/utils/validate'

export default {
  name: 'Login',
  data () {
    const validateUsername = (rule, value, callback) => {
      callback()
    }
    const validatePassword = (rule, value, callback) => {
      callback()
    }
    return {
      loginForm: {
        username: 'admin',
        password: '111111'
      },
      loginRules: {
        username: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      },
      loading: false,
      passwordType: 'password',
      redirect: undefined
    }
  },
  watch: {
    $route: {
      handler: function (route) {
        this.redirect = route.query && route.query.redirect
      },
      immediate: true
    }
  },
  methods: {
    showPwd () {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
      this.$nextTick(() => {
        this.$refs.password.focus()
      })
    },
    handleLogin () {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true
          this.$store.dispatch('user/login', this.loginForm).then(() => {
            this.$router.push({ path: this.redirect || '/' })
            this.loading = false
          }).catch(() => {
            this.loading = false
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    }
  }
}
</script>
<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f4f4f4;
}

.login-wrapper {
  background-color: #fff;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  width: 300px;
  text-align: center;
}

.header {
  font-size: 24px;
  margin-bottom: 20px;
  color: #333;
}

.form-wrapper {
  text-align: left;
}

.input-item {
  margin-bottom: 20px;
}

.btn {
  width: 100%;
}
</style>
