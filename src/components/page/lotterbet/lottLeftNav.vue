<template lang='jade'>
<!-- 我的投注 -->
div
  .lott-left-nav
    button(@click='lottnavleft')
      span
        i.el-icon-caret-left
    .lott-left-navBox(ref='lottnavbox')
      ul(ref='lottnavUl', :style="{transform: 'translateX(' + left + 'px)'}")
        li(ref='lottnavLi',:class='index == lottNameIndex? "active":""', :style="lottLeftNavBoxLi(index)", v-for='(item,index) in lotteryList', :key='index', @click='lottListNav(item,index)') {{item.name}}
    button(@click='lottnavright')
      span
        i.el-icon-caret-right
</template>
<script>
import { baseUrl } from "../../../assets/js/env";
export default {
  data() {
    return {
      GSN: JSON.parse(localStorage.getItem("globalstyle")),
      num: 0,
      left: 0,
      sum: 0,
      navTo: 0,
      playNum: 0,
      current_player: {}, //當前玩法
      current_player_bonus: {}, //當前玩法
      className: "k3_star1", //玩法ID
      lotteryList: null,
      arrLottId: [],
      arrLottName: [],
      lottNameIndex: 0,
      historyNum: 0
    };
  },
  beforeDestroy() {
    this.historyNum = 0;
    this.$store.state.historyNum = 0;
  },
  mounted() {
    this.lotteryAll();
  },
  methods: {
    lottLeftNavBoxLi(index){
      if(this.GSN.id !=="image" && this.lottNameIndex ==index){
        return {background:this.GSN.color_hover,transition: '1.2s'}
      }else if(this.GSN.id =="image" && this.lottNameIndex ==index){
        return {backgroundImage:'url('+this.GSN.color_hover_bg+')',transition: '1.2s'}
      }
    },
    // 获取彩种
    lotteryAll() {
      if (
        localStorage.getItem("lotteryAll_" + this.$route.params.id) !== null
      ) {
        this.lotteryList = JSON.parse(
          localStorage.getItem("lotteryAll_" + this.$route.params.id)
        );
        this.lotteryList.map(k => {
          this.arrLottId.push(k.id);
          this.arrLottName.push(k.name);
        });
        this.lottNameIndex = this.arrLottId.indexOf(this.$route.params.group);
        this.lottName = this.arrLottName[this.lottNameIndex];
        this.$store.commit("LOTT_NAME", this.lottName);
        if (this.lottNameIndex > 5) {
          this.left = -200;
        }
      } else {
        this.$axios
          .get(baseUrl + "/api/lottery/getLotteryList", {
            params: { type: this.$route.params.id }
          })
          .then(res => {
            localStorage.setItem(
              "lotteryAll_" + this.$route.params.id,
              JSON.stringify(res.data.data)
            );
            this.lotteryList = JSON.parse(
              localStorage.getItem("lotteryAll_" + this.$route.params.id)
            );
            this.lotteryList.map(k => {
              this.arrLottId.push(k.id);
              this.arrLottName.push(k.name);
            });
            this.lottNameIndex = this.arrLottId.indexOf(
              this.$route.params.group
            );
            this.lottName = this.arrLottName[this.lottNameIndex];
            this.$store.commit("LOTT_NAME", this.lottName);
            if (this.lottNameIndex > 5) {
              this.left = -200;
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
    },
    //导航点击
    lottListNav(item, index) {
      this.historyNum++;
      this.$store.state.historyNum = this.historyNum;
      this.lottName = this.arrLottName[this.arrLottName.indexOf(item.name)];
      this.lottNameIndex = index;
      this.$store.state.lottName = this.lottName;
      this.$emit("exit");
      this.$emit("iscreat");
      this.$router.push("/bet/" + this.$route.params.id + "/" + item.id);
      this.$parent.getPlayTree(); //玩法术
      this.$parent.geteServerTime(); //获取彩種當前獎期時間
      this.$emit("emitget"); //我的投注
    },
    //导航右边点击
    lottnavright() {
      let box = this.$refs.lottnavbox.offsetWidth;
      let ul = this.$refs.lottnavUl.offsetWidth;
      let li = this.$refs.lottnavLi[0].offsetWidth;
      if (this.num * li + box < ul) {
        this.num++;
        if (ul > box) {
          this.left = -(this.num * li);
        }
      } else if (this.num * li + box > ul) {
        this.num = this.num;
        this.$pop.show({
          title: "温馨提示",
          content: "已经到底啦",
          content1: "",
          content2: "",
          number: 2
        });
      }
    },
    //导航左边点击
    lottnavleft() {
      let box = this.$refs.lottnavbox.offsetWidth;
      let ul = this.$refs.lottnavUl.offsetWidth;
      let li = this.$refs.lottnavLi[0].offsetWidth;
      if (this.left < 0) {
        this.num = parseInt(this.left / 100 * -1);
      }
      if (this.num > 0) {
        this.num--;
        if (ul > box) {
          this.left = -(this.num * li);
        }
      } else {
        this.$pop.show({
          title: "温馨提示",
          content: "已经到顶啦",
          content1: "",
          content2: "",
          number: 2
        });
      }
    }
  }
};
</script>
<style lang='scss' scoped>
@import "@/assets/scss/lotterbet/lottlist.scss";
</style>
