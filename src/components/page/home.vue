<template lang="jade">
.home
  .homeBox
    GeminiScrollbar.my-scroll-bar(style="height:462px;width:232px;")
      .lotteList
        ul
          li(:class="nav.toF5money ? 'active' : ''", v-for='(nav,index) in lotteryList', :key='index', @click="lotteryTo(nav,index)",@mouseover="selectStyle(nav)", @mouseout="outStyle(nav)") 
            img(:class="nav.toF5money ? 'totransition' : ''", :src='"@/assets/img/lott/"+nav.groupId+".png"' alt='') 
            | {{nav.name}}
            span {{nav.time}}
    .homeSideCenter
      .banner
        .bannerBox
          router-link(to='/activity')
            img(src='@/assets/img/lott/sy1.jpg', alt='')
      .autoTab
        ul.tab
          li(:class="{'active': index === navNum}", v-for='(nav,index) in listnav', :key='index', @click='navTo($event,index,nav)') {{nav.name}}
        .autoTabContent
          ul.contentK3(v-if='lotteryId==="dfk3"')
            li
              img(:src='"@/assets/img/lott/k3n"+pastOpenK3.n1+".png"' , alt='')
              span +
              img(:src='"@/assets/img/lott/k3n"+pastOpenK3.n2+".png"' , alt='')
              span +
              img(:src='"@/assets/img/lott/k3n"+pastOpenK3.n3+".png"' , alt='')
              span =
              b {{pastOpenK3.n1+pastOpenK3.n2+pastOpenK3.n3}}
              button(:style='autoTabContentStyle',@click='gotBet') 立即投注
            li 
              span 当前：第
                em {{pastOpenK3.seasonId}} 期
              span 开奖号码
                em {{pastOpenK3.n1}},{{pastOpenK3.n2}},{{pastOpenK3.n3}}
              span 和值
                em {{pastOpenK3.n1+pastOpenK3.n2+pastOpenK3.n3}}
          ul.contentSSC(v-if='lotteryId==="sj1fc" || lotteryId==="cqssc"')
            li
              span.number {{pastOpenSSC.n1}}
              span.number {{pastOpenSSC.n2}}
              span.number {{pastOpenSSC.n3}}
              span.number {{pastOpenSSC.n4}}
              span.number {{pastOpenSSC.n5}}
              button(:style='autoTabContentStyle',@click='gotBet') 立即投注
            li 
              span 当前：第
                em {{pastOpenSSC.seasonId}} 期
              span 开奖号码
                em {{pastOpenSSC.n1}},{{pastOpenSSC.n2}},{{pastOpenSSC.n3}},{{pastOpenSSC.n4}},{{pastOpenSSC.n5}}
    .homeSideRight
      .userNameBox(v-show='!this.$store.state.loginStatus')
        button(:style='userNameBoxStyle',@click='login') 登录
        button(:style='userNameBoxStyle',@click='signin') 注册
      .userNameBox(v-show='this.$store.state.loginStatus')
        p 账号：{{Globalusername}}
      winning(:num='3')
      .winMsg
        h3 中奖信息
        .winMsgScroll
          win-msg
          .winMsgItem
</template>
<script>
import { baseUrl } from "../../assets/js/env";
import winning from "@/components/cp/winning.vue";
import winMsg from "@/components/cp/winMsg.vue";
// import { time } from "../../../static/js/times.js";
import { globalstyle } from '../../assets/js/globalstyle.js'
export default {
  data() {
    return {
      GSN: JSON.parse(localStorage.getItem("globalstyle")),
      navNum: 0,
      toF5money: false, //刷新动画
      Globalusername: this.$store.state.Globalusername,
      lotteryList: [],
      lotteryId: "dfk3", //默认上期开奖结果（江苏快3）
      pastOpenK3: { n1: 1, n2: 2, n3: 3, seasonId: 123456 }, //上期k3开奖结果
      pastOpenSSC: { n1: 1, n2: 2, n3: 3, n4: 4, n5: 5, seasonId: 123456 }, //上期ssc开奖结果
      listnav: [
        { name: "宏發快3", lotteryId: "dfk3" },
        { name: "宏發时时彩", lotteryId: "sj1fc" },
        { name: "重庆时时彩", lotteryId: "cqssc" }
      ],
      betFun: [],
      idArr: [],//所有快3
      idArrs: [],//非快3
      arrpeilva: [],
      arrpeilvb: [],
      arrpeilvc: [],
      player_bonus: [],
      playGroups: []
    };
  },
  created(){
    this.caht();
  },
  computed:{
    autoTabContentStyle(){
      if(this.GSN.id =="image"){
        return {backgroundImage:"url("+this.GSN.color_btn_bg+")"}
      }else if(this.GSN.id !=="image"){
        return {background:this.GSN.color_btn}
      }
    },
    userNameBoxStyle(){
      if(this.GSN.id =="image"){
        return {backgroundImage:"url("+this.GSN.color_btn_bg+")"}
      }else if(this.GSN.id !=="image"){
        return {background:this.GSN.color_boss}
      }
    }
  },
  mounted() {
    this.caht();
    this.getPastOp();
    this.lotteryAll();
  },
  methods: {
    caht(){
      if(localStorage.getItem("globalstyle") == null){
        localStorage.setItem("globalstyle",JSON.stringify(globalstyle[0]));
      }
    },
    fn(obj) {
      return this.$axios.get(
        baseUrl + "/api/lottery/getPlayTree?lotteryId=" + obj
      );
    },
    beilv(item, xxk3) {
      localStorage.setItem(
        "getPlayTree_playGroups_gsk3",
        JSON.stringify(item.data.data.playGroups)
      );
      localStorage.setItem(
        "getPlayTree_playBonus_gsk3",
        JSON.stringify(item.data.data.playBonus)
      );
      this.playGroups = JSON.parse(
        localStorage.getItem("getPlayTree_playGroups_" + xxk3)
      );
      this.player_bonus = JSON.parse(
        localStorage.getItem("getPlayTree_playBonus_" + xxk3)
      );
      let arr1 = [];
      let arr2 = [];
      let arrpeilv1 = this.player_bonus[3].bonusArray;
      let arrpeilv2 = this.player_bonus[4].bonusArray;
      for (let i in arrpeilv1) {
        this.arrpeilva.push(arrpeilv1[i]);
      }
      for (let i = 0; i < this.arrpeilva.length / 2; i++) {
        arr1.push(this.arrpeilva[i]);
      }
      for (let i = this.arrpeilva.length / 2; i < this.arrpeilva.length; i++) {
        arr2.push(this.arrpeilva[i]);
      }
      for (let i in arrpeilv2) {
        this.arrpeilvb.push(arrpeilv2[i]);
      }
      this.arrpeilvc.push(arr1);
      this.arrpeilvc.push(arr2);
      this.arrpeilvc.push(this.arrpeilvb);
      for (let i = 0; i < this.playGroups.length; i++) {
        for (let j = 0; j < this.playGroups[i].groups.length; j++) {
          for (
            let k = 0;
            k < this.playGroups[i].groups[j].players.length;
            k++
          ) {
            for (
              let l = 0;
              l < this.playGroups[i].groups[j].players[0].numView.length;
              l++
            ) {
              for (
                let m = 0;
                m <
                this.playGroups[i].groups[j].players[0].numView[l].nums.length;
                m++
              ) {
                if (i === 2) {
                  this.playGroups[2].groups[j].players[0].numView[l].nums[m][
                    "lottRot"
                  ] = this.arrpeilvc[l][m];
                }
              }
            }
          }
        }
      }
      localStorage.setItem(
        "getPlayTree_playGroups_" + xxk3,
        JSON.stringify(this.playGroups)
      );
    },
    //玩法术
    getPlayTree() {
      if (
        this.$store.state.loginStatus &&
        localStorage.getItem("getPlayTree_playGroups_dfk3") == null
      ) {
        this.idArr.forEach(item => {
          this.betFun.push(this.fn(item));
        });
        this.idArrs.forEach(item => {
          this.betFun.push(this.fn(item));
        });
        this.$axios.all([...this.betFun]).then(
          this.$axios.spread((...res) => {
            res.forEach(item => {
              if (item.data.status !== "302") {
                this.idArr.forEach(k => {
                  if (item.data.data.lotteryId === k) {
                    localStorage.setItem(
                      "getPlayTree_playGroups_" + k,
                      JSON.stringify(item.data.data.playGroups)
                    );
                    localStorage.setItem(
                      "getPlayTree_playBonus_" + k,
                      JSON.stringify(item.data.data.playBonus)
                    );
                    this.beilv(item, k);
                  }
                });
                this.idArrs.forEach(k => {
                  if (item.data.data.lotteryId === k) {
                    localStorage.setItem(
                      "getPlayTree_playGroups_" + k,
                      JSON.stringify(item.data.data.playGroups)
                    );
                  }
                });
              }
            });
          })
        );
      }
    },
    lotteryTo(item, index) {
      this.$router.push("/bet/" + item.groupId + "/" + item.id);
    },
    selectStyle(item) {
      this.$nextTick(() => {
        this.$set(item, "toF5money", true);
      });
    },
    outStyle(item) {
      this.$set(item, "toF5money", false);
    },
    login() {
      this.$router.push({ path: "/login/ashore" });
    },
    signin() {
      this.$router.push({ path: "/login/register" });
    },
    toActive() {
      this.$router.push({ path: "/activity" });
    },
    navTo(e, index, navs) {
      this.navNum = index;
      this.lotteryId = navs.lotteryId;
      this.getPastOp();
    },
    gotBet() {
      if (this.$store.state.loginStatus) {
        if (this.lotteryId !== "dfk3") {
          this.$router.push("/bet/ssc/" + this.lotteryId);
        } else{
          this.$router.push("/bet/k3/" + this.lotteryId);
        }
      } else {
        this.$router.push("/login/ashore");
      }
    },
    //过期判断
    isExperid() {
      let item = localStorage.getItem(this.lotteryId);
      if (item) {
        item = JSON.parse(item);
        let lastTime = item.lastTime;
        let now = Date.parse(new Date()) / 1000;
        let restSeconds = item.restSeconds;
        if (now - lastTime >= restSeconds) {
          return true;
        } else {
          return false;
        }
      } else {
        return true;
      }
    },
    //获取过去开奖号码1个
    getPastOp() {
      this.$axios
        .get(
          baseUrl +
            "/api/lottery/getPastOpen?lotteryId=" +
            this.lotteryId +
            "&count=1"
        )
        .then(res => {
          if (this.lotteryId !== "dfk3") {
            this.pastOpenSSC = res.data.data[0];
            this.lotteryId = this.pastOpenSSC.lotteryId;
          } else{
            this.pastOpenK3 = res.data.data[0];
            this.lotteryId = this.pastOpenK3.lotteryId;
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    // 获取热门彩种
    lotteryAll() {
      if (localStorage.getItem("lotteryAll_hot") !== null) {
        this.lotteryList = JSON.parse(localStorage.getItem("lotteryAll_hot"));
        for (let i = 0; i < this.lotteryList.length; i++) {
          this.lotteryList[i]["time"] = time[this.lotteryList[i].id];
          if (this.lotteryList[i].groupId === "k3") {
            this.idArr.push(this.lotteryList[i].id);
          } else {
            this.idArrs.push(this.lotteryList[i].id);
          }
        }
        if (
          this.$store.state.loginStatus &&
          localStorage.getItem("getPlayTree_playGroups_dfk3") == null
        ) {
          this.getPlayTree();
        }
      } else {
        this.$axios
          .get(baseUrl + "/api/lottery/getLotteryList")
          .then(res => {
            localStorage.setItem(
              "lotteryAll_hot",
              JSON.stringify(res.data.data)
            );
            this.lotteryList = JSON.parse(
              localStorage.getItem("lotteryAll_hot")
            );
            for (let i = 0; i < this.lotteryList.length; i++) {
              this.lotteryList[i]["time"] = time[this.lotteryList[i].id];
              if (this.lotteryList[i].groupId === "k3") {
                this.idArr.push(this.lotteryList[i].id);
              } else {
                this.idArrs.push(this.lotteryList[i].id);
              }
            }
            if (
              this.$store.state.loginStatus &&
              localStorage.getItem("getPlayTree_playGroups_dfk3") == null
            ) {
              this.getPlayTree();
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
    }
  },
  components: {
    winning,
    winMsg
  },
  filters: {
    mask(value) {
      if (!value) return "";
      value = value.toString();
      return value.charAt(0) + "***" + value.charAt(value.length - 1);
    },
    keepTwoNum(value) {
      value = Number(value);
      return value.toFixed(2);
    }
  }
};
</script>
<style lang="scss" scoped>
@import "../../assets/scss/home.scss";
</style>
