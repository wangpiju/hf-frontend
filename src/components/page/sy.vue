<template lang='jade'>
.sy
  header
    .header-box
      .header-top
        p Hi,欢迎来到业界最专业的彩票投注平台--宏發娛樂！
        ul.header-top-nologin(v-show='!$store.state.loginStatus')
          router-link(to='/login/ashore', tag='li')
            a(href='javascript:;') 亲，请登陆
          router-link(to='/login/register', tag='li')
            a(href='javascript:;') 用户注册
          router-link(to='', tag='li')
            a(href='javascript:;') 在线客服
        ul.header-top-login(v-show='$store.state.loginStatus')
          router-link(to='/user', tag='li')
            img(:src='"@/assets/img/heads/"+this.$store.state.img+".jpg"', alt='')
            span {{this.$store.state.Globalusername}}
          router-link(to='/user/notice', tag='li', @mouseover.native='HoverShowLetter=true', @mouseout.native='HoverShowLetter=false')
            span.letter(:class='letterClass',:style='letterStyle') {{count}}
          router-link(to='', tag='li', @mouseover.native='HoverShowAccount=true', @mouseout.native='HoverShowAccount=false')
            a(href='javascript:;')
              | 我的账户
              i.iconfont.icon-down1(style='font-size:14px')
            .accountList.HoverShowContent(v-if='HoverShowAccount')
              i
              div
                router-link(:to='item.path', v-for='(item,index) in acountlists', :key='index',v-if='item.choose === true')
                  | {{item.title}}
          li
            | 余额：
            span.GetMoney.getMoney(v-if='refresh')
              em {{$store.state.balance}}
              i.icon.iconfont.icon-shuaxin(:class='{totransition:clickFlag}', @click.stop='getBalance')
              i(@click.stop='changReferesh') 隐藏
            span.ShowMoney.showMoney(v-else='', @click.stop='changReferesh')
              | 已隐藏
              i 显示
          router-link(to='', tag='li', @mouseover.native='HoverShowContent=true', @mouseout.native='HoverShowContent=false')
            a(href='javascript:;')
              | 充值
              i.iconfont.icon-down1(style='font-size:14px')
            .accountList.HoverShowContent(v-if='HoverShowContent')
              i
              div
                router-link(:to='"/money/charge?id="+item.id+"&alias="+item.alias', v-for='(item,index) in paywaylist', :key='index')
                  | {{item.alias}}
          router-link(to='/money/withdraw', tag='li')
            a(href='javascript:;') 提现
          li(@click='logOut')
            a(href='javascript:;') 退出
          router-link(to='', tag='li')
            a(href='javascript:;') 在线客服
  .content-box
    .navs-box(:class='navsBoxClass',:style='navsBoxStyle')
      .navs
        .login
          router-link(to='/home')
            img(src='../../assets/img/home/logo.png', alt='')
        ul
          li(:style="navUlStyle(index)",v-for='(nav,index) in navs', :key='index',@click='navsball(nav,index)') {{nav.title}}
    .content
      .center
        router-view
  footer-bar
</template>
<script>
import { baseUrl } from "../../assets/js/env";
import footerBar from "./footer";
import { mapState, mapMutations } from "vuex";
import { setCookie, getCookie, clearCookie } from "../../assets/js/utils";
import { globalstyle } from '../../assets/js/globalstyle.js'
export default {
  data() {
    return {
      GSN:null,
      img: 0,
      active: 0,
      refresh: false,
      clickFlag: false,
      HoverShowContent: false,
      HoverShowAccount: false,
      HoverShowLetter: false,
      acountlists: [
        { title: "投注记录",choose:true, path: "/betManage/betRecord" },
        { title: "交易记录",choose:true, path: "/user/billRecord" },
        { title: "个人信息",choose:true, path: "/user/userinfo" },
        { title: "安全中心",choose:true, path: "/user/securityCenter" },
        { title: "代理中心",choose:false, path: "/agent/agentReport" }
      ],
      navs: [
        { title: "首页", path: "/home" },
        { title: "彩票大厅", path: "/lott" },
        { title: "活动中心", path: "/activity" },
        { title: "手机购彩", path: "/appdown" },
        { title: "帮助指南", path: "/helpcenter" }
      ],
      paywaylist: [],
      localStorageArr: [],
      count: 0
    };
  },
  beforeCreate(){
    localStorage.setItem("globalstyle",JSON.stringify(globalstyle[0]));
  },
  created(){
    this.GSN = JSON.parse(localStorage.getItem("globalstyle"));
  },
  computed: {
    letterClass(){
      if(this.GSN.id !=="image" && this.HoverShowLetter ==true){
        return "color_boss_bg"
      }else if(this.GSN.id !=="image" && this.HoverShowLetter ==false){
        return "color_hover_bg"
      }else if(this.GSN.id =="image" && this.HoverShowLetter ==true){
        return "color_boss"
      }else if(this.GSN.id =="image" && this.HoverShowLetter ==false){
        return "color_hover"
      }
    },
    letterStyle(){
      if(this.GSN.id !=="image" && this.HoverShowLetter ==false){
        return {background:this.GSN.color_boss}
      }else if(this.GSN.id !=="image" && this.HoverShowLetter ==true){
        return {background:this.GSN.color_hover}
      }else if(this.GSN.id =="image" && this.HoverShowLetter ==false){
        return {backgroundImage:"url("+this.GSN.color_boss_bg+")"}
      }else if(this.GSN.id =="image" && this.HoverShowLetter ==true){
        return {backgroundImage:"url("+this.GSN.color_hover_bg+")"}
      }
    },
    navsBoxClass() {
      return this.GSN.id !== "image" ? "color_boss_bg_" + this.GSN.id : "color_boss_bg_" + this.GSN.id;
    },
    navsBoxStyle() {
      return this.GSN.id == "image" ? { backgroundImage: "url(" + this.GSN.color_boss_bg + ")" } : { background: this.GSN.color_boss };
    },
  },
  mounted() {
    this.getRechargeWayList();
    this.getTopUserData();
    this.getNoReadNoticeStatus();
    this.noMember();
  },
  methods: {
    //非会员不显示代理
    noMember(){
      this.acountlists.forEach(el => {
        if(this.$store.state.userType === '0'){
          if(el.title === '代理中心'){
            el.choose = false;
          }
        }else if(this.$store.state.userType === '1'){
          if(el.title === '代理中心'){
            el.choose = true;
          }
        }
      });
    },
    //导航点击背景色
    navUlStyle(index){
      if(this.GSN.id !=="image" && this.active ==index){
        return {background:this.GSN.color_hover,transition: '1.2s'}
      }else if(this.GSN.id =="image" && this.active ==index){
        return {backgroundImage:'url('+this.GSN.color_hover_bg+')',transition: '1.2s'}
      }
    },
    //导航点击事件
    navsball(nav,index){
      this.$router.push(nav.path);
      this.active = index;
    },
    changReferesh() {
      this.refresh = !this.refresh;
    },
    getBalance() {
      this.clickFlag = true;
      this.$axios
        .get(baseUrl + "/api/userCenter/getBalance")
        .then(res => {
          this.clickFlag = false;
          this.$store.state.balance = res.data.data.balance;
        })
        .catch(error => {
          this.clickFlag = false;
          console.log(error);
        });
    },
    setClass(v) {
      this.active = v;
    },
    ...mapMutations(["OUT_LOGIN"]),
    //获取用户信息
    getTopUserData() {
      console.log(
        "%cHi,欢迎来到业界最专业的彩票投注平台！",
        "text-shadow:0 1px 0 #ccc,0 2px 0 #c9c9c9,0 3px 0 #bbb,0 4px 0 #b9b9b9,0 5px 0 #aaa,0 6px 1px rgba(0,0,0,.1),0 0 5px rgba(0,0,0,.1),0 1px 3px rgba(0,0,0,.3),0 3px 5px rgba(0,0,0,.2),0 5px 10px rgba(0,0,0,.25),0 10px 10px rgba(0,0,0,.2),0 20px 20px rgba(0,0,0,.15);font-size:32px;"
      );
      if (localStorage.getItem("topUserData")) {
        let topUserData = JSON.parse(localStorage.getItem("topUserData"));
        this.$store.state.img = topUserData.image;
      } else {
        this.$axios
          .get(baseUrl + "/api/userCenter/getTopUserData")
          .then(res => {
            this.$store.state.img = res.data.data.image;
            localStorage.setItem("topUserData", JSON.stringify(res.data.data));
          })
          .catch(error => {
            this.$store.state.img = 0;
          });
      }
    },
    getRechargeWayList() {
      if (localStorage.getItem("paywaylist")) {
        let paywaylist = JSON.parse(localStorage.getItem("paywaylist"));
        this.paywaylist = paywaylist;
      } else {
        if (this.$store.state.loginStatus) {
          this.$axios
            .get(baseUrl + "/api/proxy/getRechargeWayList")
            .then(res => {
              this.paywaylist = res.data.data;
              localStorage.setItem("paywaylist", JSON.stringify(res.data.data));
            })
            .catch(error => {
              console.log("Error");
            });
        }
      }
    },
    logOut() {
      for (let i = 0; i < localStorage.length; i++) {
        this.localStorageArr.push(localStorage.key(i));
      }
      this.localStorageArr.map(key => {
        if (key !== "username" &&  key !== 'globalstyle') {
          localStorage.removeItem(key);
        }
      });
      this.$axios
        .get(baseUrl + "/api/user/logout")
        .then(res => {
          if (res.data.data === "已登出") {
            this.$store.state.loginStatus = false;
            localStorage.setItem("loginStatus", false);
            // localStorage.clear();
            this.$router.push("/login/ashore");
          }
        })
        .catch(error => {
          console.log("logOutNo");
        });
    },
    //判断是否有未读消息
    getNoReadNoticeStatus() {
      this.$axios
        .get(baseUrl + "/api/proxy/getNoReadNoticeStatus")
        .then(res => {
          //状态
          this.isNotice = res.data.data.flag;
          //条数
          this.count = res.data.data.count;
        })
        .catch(error => {
          console.log("获取彩種ratio ERROR");
        });
    }
  },
  components: {
    footerBar
  }
};
</script>
<style lang="scss" scoped>
@import "../../assets/scss/sy.scss";
</style>
