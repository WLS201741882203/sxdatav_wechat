<template>
  <div class="login" style="height:100%;">
    <div style="height:25%"></div>
    <div class="outer_label">
      <span>欢迎登录村务公众号平台</span>
    </div>
    <div class="login_form">
      <span>身份证号</span>
      <input type="text"  class="qxs-ic_user qxs-icon"  placeholder="用户名" v-model="loginParams.idcard">
      <br>
      <span>密 码</span>
      <input type="password"  class="qxs-ic_password qxs-icon"  placeholder="密码" v-model="loginParams.password">
      <button class="login_btn" @click="login(loginParams)" type="primary" >登录</button>
      <div style="margin-top: 10px">
        <span style="color: #000099;">账号登陆</span><span style="float: right;color: #020202">忘记密码？</span>
      </div>
    <toast v-model="showtoast" type="text" :time="800" width="100%" is-show-mask :text="showtoasttext" position="top"></toast>
    </div>
  </div>
</template>

<script>
import { Toast, TransferDom } from 'vux'
import { mapMutations } from 'vuex';
  export default {
    name: 'login',
    components: { Toast },
    directives: {
      TransferDom
    },
    data() {
      return {
        loginParams: {
        idcard: '',
        password: '',
        },
        showtoast: false,
        showtoasttext: '请填入信息',
      }
    },
    created () {
      if(JSON.parse( localStorage.getItem('user')) && JSON.parse( localStorage.getItem('user')).idcard) {
        this.idcard = JSON.parse( localStorage.getItem('user')).idcard;
        this.password = JSON.parse( localStorage.getItem('user')).password;
      }
    },
    computed: {
    },
    methods: {
        login (data) { // 数据请求函数
        var fromData = JSON.stringify(this.loginParams); //数据表单
         if (this.loginParams.idcard === '' || this.loginParams.password === '') {
          alert('帐号和密码不能为空');
        } else {
          this.axios.post('http://localhost:5050/api/person/login?',this.qs.stringify(this.loginParams)).then((res) => {
             console.log(res.data)
             console.log(this.loginParams)
             this.idcard = ''
             this.password = ''
             this.showtoasttext = '登录成功'
             this.showtoast = true
             if(this.loginParams.password === res.data.data.password) {
               this.$router.replace('/home') // 登录成功跳转到/home
               console.log('成功')
             }
          }, function () {
            console.log('请求失败处理') // 请求失败函数
          })
        } 
      },
    },
    mounted () {
      this.login()
    }
  }
</script>
<style>
  .login_form {
    padding-top: 15%;
    padding-left: 10%;
    padding-right: 10%;
  }
  .qxs-ic_user {
    /* background: url("../../assets/img/login/ic_user.png") no-repeat; */
    background-size: 13px 15px;
    background-position: 3%;
    margin-bottom: 10px;
  }
  .qxs-ic_password {
    /* background: url("../../assets/img/login/ic_password.png") no-repeat; */
    background-size: 13px 15px;
    background-position: 3%;
    margin-bottom: 20px;
  }
  .outer_label{
    text-align: center;
    font-weight: bold;
    font-size: 18px;
    margin: auto;
    color: #411100;
    width:65%;
    border-left:#ffff00 solid 3px;
  }
  .login {
    background: linear-gradient(left bottom, #f0f000,#ff0000);
    display: -moz-grid-group
  }
  .login_btn {
    display: block;
    margin: auto;
    width: 50%;
    font-size: 16px;
    background: -webkit-linear-gradient(left, #000099, #2154FA); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(right, #000099, #2154FA); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(right, #000099, #2154FA); /* Firefox 3.6 - 15 */
    background: linear-gradient(to right, #000099 , #2154FA); /* 标准的语法 */
    filter: brightness(1.4);
  }
</style>

