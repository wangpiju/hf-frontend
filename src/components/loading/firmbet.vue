<template lang='jade'>
.firmbet(v-show='toshwo')
  ul.num4
    li.title(:class='num4Class',:style='num4Style')
      span
      p 温馨提示
      span(@click='isshow')
        i.el-icon-circle-close-outline.transition
    li.betConfirmBox
      div
        p.hint 请核准您的投注信息
        p.headline
          span 彩种：{{addName}}
          span 期号：{{content}}
        .addBox
          span.addHead 详情：
          div
            ul
              li(v-for='(item,index) in productLists', :key='index')
                span.addtitle 【{{item.addTitle}}】
                p.addCon {{item.addCon}}
        p.payment
          | 付款总金额：
          i {{money}} 
          |  元
        p.user 付款账号：{{username}}
    li.dialogBtn
      button(@click='betGo') 确认投注
</template>
<script>
import { baseUrl } from "@/assets/js/env";
export default {
  data() {
    return {
      GSN: JSON.parse(localStorage.getItem("globalstyle")),
      zhu: 0,
      addMoney: 0,
      money: 0,
      toshwo: false,
      addName: null,
      username: localStorage.getItem("username"),
      conTemp: "",
      zhuTemp: "",
      betFun: [],
      dxds: ["大", "小", "单", "双"]
    };
  },
  props: {
    productLists: {
      type: Array
    },
    content: {
      type: Number
    },
    pds: {
      type: Object
    }
  },
  computed:{
    num4Class() {
      return this.GSN.id !== "image" ? "color_boss_bg_" + this.GSN.id : "color_boss_bg_" + this.GSN.id;
    },
    num4Style(){
      return this.GSN.id=="image"?{ backgroundImage:"url("+this.GSN.color_boss_bg+")" } : { background: this.GSN.color_boss };
    }
  },
  methods: {
    isshow() {
      this.toshwo = !this.toshwo;
      let addName = "";
      let addMoney = 0;
      let addzhu = 0;
      let money = 0;
      for (let i = 0; i < this.productLists.length; i++) {
        addMoney += this.productLists[i].addMoney;
        addzhu += this.productLists[i].addzhu;
        money += this.productLists[i].addMoney * this.productLists[i].addzhu;
        addName = this.productLists[0].addName;
      }
      this.money = money;
      this.addName = addName;
    },
    bet(obj) {
      if (this.$route.params.id == "k3") {
        if (
          obj.con.includes("大") ||
          obj.con.includes("小") ||
          obj.con.includes("单") ||
          obj.con.includes("双")
        ) {
          obj.addClassName = "k3_star3_big_odd";
        } else if (this.$store.state.className == "k3_star3_and") {
          obj.addClassName = "k3_star3_and";
        }
      }
      let formData = new FormData();
      formData.append("order[0].content", obj.con);
      formData.append("order[0].betCount", obj.zhu);
      formData.append("order[0].price", obj.addMoney);
      formData.append("order[0].unit", 1);
      formData.append("order[0].playId", obj.addClassName);
      formData.append("count", obj.zhu);
      formData.append("traceOrders[0].price", obj.addMoney);
      formData.append("traceOrders[0].seasonId", this.$store.state.seasonId);
      formData.append("bounsType", 0);
      formData.append("traceWinStop", 0);
      formData.append("isTrace", 0);
      formData.append("lotteryId", this.$route.params.group);
      formData.append("amount", obj.addMoney * obj.zhu);
      return this.$axios.post(
        baseUrl + "/api/lottery/bet",
        formData,
        this.$store.state.config
      );
    },
    spli(str, item) {
      this.betFun.push(
        this.bet({
          con: str,
          zhu: 1,
          addMoney: item.addMoney,
          addClassName: item.addClassName,
          addMoney: item.addMoney,
          addSeasonId: item.addSeasonId
        })
      );
      let con = this.conTemp.split(",");
      con.splice(con.lastIndexOf(str), 1);
      con = con.join(",");
      this.conTemp = con;
      this.zhuTemp -= 1;
    },
    splik3(arrk3, item) {
      let n = 0;
      arrk3.map(a => {
        this.dxds.map(b => {
          if (a == b) {
            n++;
            this.$store.state.k3conTemp.push(b);
            arrk3.splice(arrk3.indexOf(b), 1, "");
          }
        });
      });
      arrk3 = arrk3
        .filter(function(n) {
          return n;
        })
        .join(",");
      this.$store.state.k3conTemp
        .filter(function(n) {
          return n;
        })
        .join(",");
      this.$store.state.k3zhuTemp = n;
      this.betFun.push(
        this.bet({
          con: this.$store.state.k3conTemp,
          zhu: this.$store.state.k3zhuTemp,
          addMoney: item.addMoney,
          addClassName: item.addClassName,
          addMoney: item.addMoney,
          addSeasonId: item.addSeasonId
        })
      );
      this.conTemp = arrk3;
      this.zhuTemp -= n;
      this.$store.state.k3conTemp = [];
    },
    //确认投注
    betGo() {
      let addMoney = 0;
      let addzhu = 0;
      let money = 0;
      let addSeasonId = null;
      this.productLists.map(item => {
        this.conTemp = item.addCon;
        this.zhuTemp = item.addzhu;
        if (
          item.addClassName !== "k3_star3_and" &&
          this.$route.params.id !== "ssc" && this.$route.params.id !== "pk10"
        ) {
          if (item.addCon.includes("大")) {
            this.spli("大", item);
          }
          if (item.addCon.includes("小")) {
            this.spli("小", item);
          }
          if (item.addCon.includes("单")) {
            this.spli("单", item);
          }
          if (item.addCon.includes("双")) {
            this.spli("双", item);
          }
        } else if (
          item.addClassName === "k3_star3_and" &&
          this.$route.params.id !== "ssc" && this.$route.params.id !== "pk10"
        ) {
          if (
            item.addCon.includes("大") ||
            item.addCon.includes("小") ||
            item.addCon.includes("单") ||
            item.addCon.includes("双")
          ) {
            let arrk3 = item.addCon.split(",");
            this.splik3(arrk3, item);
          }
        }
        if (
          item.addCon.match(/\d/) ||
          item.addCon.includes("龙") ||
          item.addCon.includes("虎") ||
          item.addCon.includes("和") ||
          item.addCon.includes("大单") ||
          item.addCon.includes("小单") ||
          item.addCon.includes("大双") ||
          item.addCon.includes("小双")
        ) {
          this.betFun.push(
            this.bet({
              con: this.conTemp,
              zhu: this.zhuTemp,
              addMoney: item.addMoney,
              addClassName: item.addClassName,
              addMoney: item.addMoney,
              addSeasonId: item.addSeasonId
            })
          );
        }else if(this.$route.params.id == "pk10" || this.$route.params.id == "ssc"){
          this.betFun.push(
            this.bet({
              con: this.conTemp,
              zhu: this.zhuTemp,
              addMoney: item.addMoney,
              addClassName: item.addClassName,
              addMoney: item.addMoney,
              addSeasonId: item.addSeasonId
            })
          );
        }
        this.$axios.all([...this.betFun]).then(
          this.$axios.spread((...res) => {
            let showFlag = true;
            for (let item of res) {
              if (item.data.message !== "success") {
                showFlag = false;
                break;
              }
            }
            if (showFlag) {
              this.toshwo = false;
              this.$parent.getbetOrderList(); //我的投注
              this.$parent.exit(); //调用父级的清空方法
              this.$pop.show({
                title: "温馨提示",
                content: "恭喜您，投注成功！",
                content1: "",
                content2: "",
                number: 1
              });
            }
            this.betFun = [];
            this.$parent.exit(); //调用父级的清空方法
          })
        );
      });
    }
  }
};
</script>
<style lang="scss" scoped>
@import "../../assets/minix/main.scss";
.firmbet {
  position: absolute;
  top: 0px;
  left: 0px;
  z-index: 20180608;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.4);
  ul.num4 {
    min-width: 300px;
    max-width: 500px;
    width: 460px;
    height: 432px;
    background: #fff;
    border-radius: 4px;
    @extend %flex;
    @extend %fdc;
    @extend %sbetween;
    & li.title {
      @extend %flex, %aitems, %sbetween;
      color: #fff;
      font-size: 16px;
      height: 40px;
      border-radius: 4px 4px 0px 0px;
      & span {
        @extend %faj;
        width: 10%;
        height: 40px;
      }
      & p {
        @extend %faj;
        width: 80%;
        height: 40px;
      }
      & i {
        @extend %faj;
        width: 26px;
        height: 26px;
        font-size: 26px;
      }
      & i.transition:hover {
        transition: All 4s ease-in-out;
        transform: rotate(3600deg);
      }
    }
    & li.betConfirmBox {
      height: 340px;
      padding: 20px 30px 0px;
      & .hint {
        font-size: 14px;
        font-weight: 700;
        height: 30px;
      }
      & .headline {
        font-size: 14px;
        & span {
          margin-right: 20px;
        }
      }
      & .addBox {
        @extend %flex;
        margin-top: 14px;
        & .addHead {
          width: 40px;
        }
        > div {
          width: 350px;
          height: 210px;
          overflow: hidden;
          overflow-y: scroll;
          border: 1px solid #ddd;
          padding: 10px 0px 10px 10px;
          > ul {
            & li {
              @extend %flex;
              & .addtitle {
                @extend %flex;
                width: 108px;
                margin-right: 10px;
              }
              & .addCon {
                @extend %flex, %fr;
                max-width: 220px;
                word-break: break-all;
              }
            }
          }
        }
      }
      & .payment,
      .user {
        height: 30px;
        & i {
          color: red;
        }
      }
    }
    & li.dialogBtn {
      @extend %faj;
      height: 52px;
      & button {
        width: 100px;
        height: 30px;
        background:$color_btn;
        border-radius: 15px;
        color: #fff;
        font-size: 14px;
      }
    }
  }
}
</style>

