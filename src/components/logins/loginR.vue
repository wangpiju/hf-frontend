<template lang="jade">
.loginRightBox
  ul.loginRightUl
    li
      i.iconfont.icon-users
      div
        input(type='text', v-model='userinfo.inviteCode', placeholder='请输入邀请码')
    li
      i.iconfont.icon-user1
      div
        input(type='text', v-model='userinfo.username', placeholder='请输入用户名')
    li
      i.iconfont.icon-lock
      div
        input(type='text', v-model='userinfo.captcha_code', placeholder='请输入验证码')
        img(:src='captchaCodeImg', @click='getCaptchaCode')
  .registerBtn
    p
      span
        | 默认密码：
        i aa123456
      span
        | 已有账号？
        i.color(@click='toLogin') 立即登陆
    button.registerGo(color='error', @click='registerGo') 确认注册
</template>
<script>
import md5 from "js-md5";
import { mapState, mapMutations } from "vuex";
import { baseUrl } from "../../assets/js/env";
export default {
  data() {
    return {
      captchaCodeImg: "",
      newDate: null, 
      userinfo: {
        inviteCode: "",
        username: "",
        password: "aa123456",
        captcha_code: ""
      }
    };
  },
  created() {
    this.getCaptchaCode();
  },
  methods: {
    ...mapMutations(["RECORD_USERINFO"]),
    getCaptchaCode() {
      this.userinfo.inviteCode = this.$route.query.code;
      this.newDate = new Date().getTime();
      this.captchaCodeImg = baseUrl + "/code.jpg?_=" + this.newDate;
    },
    toLogin() {
      this.$router.push("/login/ashore");
    },
    registerGo() {
      let formData = new FormData();
      formData.append("inviteCode", this.userinfo.inviteCode);
      formData.append("account", this.userinfo.username);
      formData.append("password", this.userinfo.password);
      formData.append("code", this.userinfo.captcha_code);
      this.$axios
        .post(
          baseUrl + "/api/user/register",
          formData,
          this.$store.state.config
        )
        .then(res => {
          if (
            res.data.data === "验证码错误！" ||
            res.data.data !== "注册成功!"
          ) {
            this.getCaptchaCode();
          }
          if (res.data.data === "注册成功!") {
            this.$message(res.data.data);
            setTimeout(() => {
              let pwd1 = md5(this.userinfo.password);
              let formData1 = new FormData();
              formData1.append("account", this.userinfo.username);
              formData1.append("password", pwd1);
              formData1.append("code", "");
              this.$axios
                .post(
                  baseUrl + "/api/user/login",
                  formData1,
                  this.$store.state.config
                )
                .then(res => {
                  if (res.data.pup === true) {
                    this.$message(res.data.data);
                  }
                  if (res.data.message === "success") {
                    this.$store.state.loginStatus = true;
                    localStorage.setItem(
                      "loginStatus",
                      this.$store.state.loginStatus
                    );
                    this.$store.state.Globalusername = this.userinfo.username;
                    localStorage.setItem("username", this.$store.state.Globalusername);
                    this.$router.push({ name: "sy" });
                    this.RECORD_USERINFO(this.userinfo);
                  }
                });
            }, 600);
          }
        });
    }
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/scss/login.scss";
.el-message-box__content {
  background: red;
}
.registerGo {
    width: 340px;
    margin-top: 20px;
    background: #db544a;
    font-size: 14px;
    min-width: 88px;
    height: 36px;
    line-height: 36px;
    border:1px #db544a;
    border-radius: 2px;
    background-color: #fff;
    color: #eee;
    box-shadow: 0 3px 1px -2px rgba(0,0,0,.2), 0 2px 2px 0 rgba(0,0,0,.14), 0 1px 5px 0 rgba(0,0,0,.12);
}
</style>


