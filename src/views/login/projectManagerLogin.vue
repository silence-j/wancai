<template>
  <el-row class="panel">
    <el-col :span="24" class="panel-top">
      <logo></logo>
    </el-col>
    <el-col :span="14"  class="panel-ads">
    </el-col>
    <el-col :span="10"  class="panel-login" >
      <el-form :model="loginForm" :rules="loginRules" ref="loginForm">
        <h3 class="loginTitle">登 录</h3>
        <el-form-item prop="account">
          <el-input type="text" v-model="loginForm.account" auto-complete="off" placeholder="请输入注册手机号"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="请输入密码"></el-input>
        </el-form-item>
        <div class="passwordFind">
          <router-link :to="{ name: 'managerPasswordFind'}" >忘记密码</router-link>
        </div>
        <el-button type="success" @click.native.prevent="handleSubmit" :loading="logining">登 录</el-button>
      </el-form>
    </el-col>
  </el-row>
</template>
<script>
import logo from '../../components/logo';

export default {
  name: 'projectManagerLogin',
  components: {
    logo,
  },
  data() {
    return {
      logining: false,
      loginForm: {
        account: '',
        password: '',
      },
      loginRules: {
        account: [
          { required: true, message: '请输入注册手机号', trigger: 'blur' },
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
        ],
      },
    };
  },
  methods: {
    handleSubmit() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.logining = true;
          const params = new URLSearchParams();
          params.append('u', this.loginForm.account);
          params.append('p', this.loginForm.password);

          this.$http.post(`${this.$managerURL}/login`, params, {
            headers: {
              'Content-Type': 'application/x-www-form-urlencoded',
              // 'Content-Type': 'application/json',
            },
          }).then((response) => {
            // eslint-disable-next-line
            if (response.data.errorCode === 10000) {
              this.$message({
                message: '登录成功',
                type: 'success',
              });
              sessionStorage.setItem('project_manager', JSON.stringify(response.data));
              this.$router.replace({ path: '/project_manager' });
            } else {
              this.$message.error('账号或密码错误');
            }
            this.logining = false;
          }).catch((error) => {
            this.$message.error('登录异常');
            this.logining = false;
          });
        }
        return false;
      });
    },
  },
  mounted() {
    const projectManager = sessionStorage.getItem('project_manager');
    if (projectManager) {
      this.$router.push('');
    }
  },
};
</script>
<style lang='scss' scoped>
  .panel {
    position: absolute;
    top: 0px;
    bottom: 0px;
    width: 100%;

    .panel-top {
      height: 60px;
      background: #EEF1F6;
    }
    .panel-ads {
      position: absolute;
      // background: #d1a2d6;
      top: 60px;
      bottom: 0px;
      overflow: hidden;
    }
    .panel-login {
      // background: #d1a2d6;
      position: absolute;
      right: 0px;
      top: 60px;
      bottom: 0px;
      display: flex;
      flex-direction: column;
      align-items: center;
      // background: #00BFFF;
      .el-form {
        display: flex;
        flex-direction: column;
        justify-content: center;
        margin-top: 150px;
        width: 50%;

        .loginTitle {
          margin: 0 auto 20px auto;
          text-align: center;
          color: #a7b8ca
        }

        > div:nth-child(3) {
          margin-bottom: 10px;
        }
        .passwordFind {
          text-align: right;
          // margin-top: 10px;
          margin-bottom: 10px;
          > a {
            text-decoration: none;
            color: #49a3e5;
          }
        }
      }
    }
  }
</style>
