<template lang='jade'>
<!-- 选号模块11x5 -->
div
  div(v-if='showhaa')
  .getPlayTree
    ul
      li(:class="{'active': index === navTo}", v-for='(item,index) in playGroups', :key='index', @click='playGroupBut(item,index)') {{item.title}}
  .getPlayTreeBox
    ul
      li(v-for='(item,indexs) in playGroups', v-if='indexs === navTo')
        div(v-for='(group,indexabc) in item.groups')
          span.groupTitle {{group.title}}
          span.groupTitleList(:class="{'active': current_player_bonus.id=== player.id}", v-for='(player,indexbcd) in group.players', v-if="player.id!='n11x5_star3_big'&&player.id!='n11x5_star3_small'&&player.id!='n11x5_star3_odd'&&player.id!='n11x5_star3_even'", @click='playersBut(player,indexbcd,indexabc)') {{player.title}}
  .conterButBox
    .conterButTitle
      i.el-icon-info
      | {{current_player_bonus.remark}}。单注最高奖金
      i(v-show='Number(current_player_bonus.displayBonus)') {{current_player_bonus.displayBonus | keepTwoNum}}
      i(v-show='isNaN(current_player_bonus.displayBonus)') {{displayBonus1 | keepTwoNum}}—{{displayBonus2 | keepTwoNum}}
      | 倍
    .conterBut(:class="'conterBut'+className")
      .conterButDiv(:class="className+'Box'", v-if="current_player_bonus && current_player_bonus.id === 'n11x5_star3_and'")
        .both
          span.carTitle 中三和值
          .carBox
            .cars
              p.car(:class="[item.choose ? 'active' : '',className]", v-for='(item,indexha) in star3_and', @click='curBalls(0,indexha,item,star3_and,current_player_bonus)')
                span
                  i {{item.ball}}
      .conterButDiv(:class="className+'Box'", v-for='(numViews, indexf) in current_player_bonus.numView', v-else='')
        .both
          span.carTitle(:class="{'active': numViews.title === ''}") {{numViews.title}}
          .carBox
            .cars
              p.car(:class="[item.choose ? 'active' : '',className]", v-for='(item,indexha) in numViews.nums', :key='indexha', @click='curBalls(indexf,indexha,item,numViews,current_player_bonus)')
                span
                  i {{item.ball}}
            .changes(v-if='numViews.title !== "胆码" && className !== "n11x5_dragon_lion"')
              span(v-for='(tools,indexto) in ballTools', :key='indexto', @click='toolsCur(tools,indexto,numViews,indexf,current_player_bonus)') {{tools.name}}      
</template>
<script>
import { baseUrl } from "../../../assets/js/env";
export default {
  data() {
    return {
      showhaa: true,
      navTo: 0,
      playNum: 0,
      playNums: false,
      className: "n11x5_x1", //玩法ID
      lotteryId: "sh11x5", //彩种id
      lottNameIndex: 3, //默认彩种
      bonusArray: [], //和值赔率
      current_player: {}, //當前玩法
      current_player_bonus: {}, //當前玩法
      an: "",
      bn: "",
      cn: "",
      dn: "",
      en: "",
      con: "",
      addTitle: "任选一任选复式",
      d: [], //选中的号码的下标
      ka: [], //选中的号码的下标
      kb: [], //选中的号码的下标
      kc: [], //选中的号码的下标
      kd: [], //选中的号码的下标
      ke: [], //选中的号码的下标
      dm: "", //胆码
      tm: "", //拖码
      dmNum: "", //可选择胆码数量
      dmArr: [], //最新选中胆码下标
      item: {},
      indexha: 0,
      ballTools: [
        { fncode: "full", name: "全", choose: false },
        { fncode: "big", name: "大", choose: false },
        { fncode: "small", name: "小", choose: false },
        { fncode: "single", name: "单", choose: false },
        { fncode: "double", name: "双", choose: false },
        { fncode: "empty", name: "清", choose: false }
      ],
      displayBonus: 0,
      displayBonus1: 0,
      displayBonus2: 0,
      displayBonus3: ""
    };
  },
  beforeDestroy() {
    this.iscreat();
  },
  computed: {
    star3_and() {
      let arr = [];
      this.current_player_bonus.numView.forEach((item, index) => {
        arr.push(...item.nums);
      });
      return arr;
    },
    playGroups() {
      return JSON.parse(
        localStorage.getItem(
          "getPlayTree_playGroups_" + this.$route.params.group
        )
      );
    },
  },
  mounted() {
    if (
      localStorage.getItem(
        "getPlayTree_playGroups_" + this.$route.params.group
      ) !== null
    ) {
      this.isShowPlayGroups();
    } else {
      setTimeout(() => {
        this.isShowPlayGroups();
      }, 600);
    }
  },
  methods: {
    //判断玩法术是否已经成功
    isShowPlayGroups() {
      this.showhaa = false;
      this.current_player_bonus = JSON.parse(
        localStorage.getItem(
          "getPlayTree_playGroups_" + this.$route.params.group
        )
      )[0].groups[0].players[0];
      this.$store.state.className = this.current_player_bonus.id;
      this.className = this.current_player_bonus.id;
    },
    //中间->投注选号
    curBalls(indexff, indexg, num, numViews, player) {
      if (numViews.chooseOne) {
        numViews.balls.map(b => {
          b.choose = false;
        });
      }
      num.choose = !num.choose;
      if (num.choose === true) {
        this.d[indexg] = num.ball;
        this.dd = this.d.filter(function(n) {
          return n;
        });
        this.$store.state.zhu++;
        this.$store.state.pd.addTitle = this.addTitle;
        this.$store.state.pd.addCon = this.dd.join(",");
        this.$store.state.con = this.dd.join(",");
        this.$store.state.pd.addPattern = "元";
        this.$store.state.pd.addzhu = this.$store.state.zhu;
        this.$store.state.pd.addMoney = this.$store.state.spinner3;
        this.$store.state.pd.addClassName = this.className;
        this.$store.state.pd.addSeasonId = this.$store.state.seasonId;
        this.$store.state.pd.addName = this.$store.state.lottName;
        this.bet_boxjia(indexff, indexg, num, numViews, player);
      } else {
        this.d.splice(indexg, 1, "");
        this.dd = this.d.filter(function(n) {
          return n;
        });
        this.$store.state.zhu--;
        this.$store.state.con = this.dd.join(",");
        this.$store.state.pd.addCon = this.dd.join(",");
        this.bet_boxjian(indexff, indexg, num, numViews, player);
      }
    },
    //复式投注
    fushi() {
      let ret = "";
      if (
        this.className === "n11x5_x2" ||
        this.className === "n11x5_star2_group"
      ) {
        ret = this.groupSplit(this.dd, 2);
      }
      if (
        this.className === "n11x5_x3" ||
        this.className === "n11x5_star3_group"
      ) {
        ret = this.groupSplit(this.dd, 3);
      }
      if (this.className === "n11x5_x4") {
        ret = this.groupSplit(this.dd, 4);
      }
      if (this.className === "n11x5_x5") {
        ret = this.groupSplit(this.dd, 5);
      }
      if (this.className === "n11x5_x6") {
        ret = this.groupSplit(this.dd, 6);
      }
      if (this.className === "n11x5_x7") {
        ret = this.groupSplit(this.dd, 7);
      }
      if (this.className === "n11x5_x8") {
        ret = this.groupSplit(this.dd, 8);
      }
      this.$store.state.zhu = ret.length;
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //投注 ++++
    bet_boxjia(indexff, indexg, num, numViews, player) {
      //任选复式、不定位
      if(this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1"){
        this.ka[indexg] = num.ball;
        this.dd = this.ka.filter(function(n) {return n;});
        this.an = this.dd.join(",");
        this.$store.state.con =this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选一定位胆 ++
      if (this.className === "n11x5_dwd") {
        if (indexff === 0) {
          this.ka[indexg] = num.ball;
          this.dd = this.ka.filter(function(n) {
            return n;
          });
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb[indexg] = num.ball;
          this.dd = this.kb.filter(function(n) {
            return n;
          });
          this.bn = this.dd.join("");
        }
        if (indexff === 2) {
          this.kc[indexg] = num.ball;
          this.dd = this.kc.filter(function(n) {
            return n;
          });
          this.cn = this.dd.join("");
        }
        if (indexff === 3) {
          this.kd[indexg] = num.ball;
          this.dd = this.kd.filter(function(n) {
            return n;
          });
          this.dn = this.dd.join("");
        }
        if (indexff === 4) {
          this.ke[indexg] = num.ball;
          this.dd = this.ke.filter(function(n) {
            return n;
          });
          this.en = this.dd.join("");
        }
        if (this.an === "") {
          this.an = "-";
        }
        if (this.bn === "") {
          this.bn = "-";
        }
        if (this.cn === "") {
          this.cn = "-";
        }
        if (this.dn === "") {
          this.dn = "-";
        }
        if (this.en === "") {
          this.en = "-";
        }
        this.$store.state.con =
          this.an +
          "," +
          this.bn +
          "," +
          this.cn +
          "," +
          this.dn +
          "," +
          this.en;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8") {
        this.ka[indexg] = num.ball;
        this.dd = this.ka.filter(function(n) {return n;});
        this.an = this.dd.join(",");
        this.fushi();
        this.$store.state.con =this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二直选复式 ++
      if (this.className === "n11x5_star2_front") {
        if (indexff === 0) {
          this.ka[indexg] = num.ball;
          this.dd = this.ka;
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb[indexg] = num.ball;
          this.dd = this.kb;
          this.bn = this.dd.join("");
        }
        let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
        this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;
      }
      //选三直选复式 ++
      if (this.className === "n11x5_star3_front") {
        if (indexff === 0) {
          this.ka[indexg] = num.ball;
          this.dd = this.ka;
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb[indexg] = num.ball;
          this.dd = this.kb;
          this.bn = this.dd.join("");
        }
        if (indexff === 2) {
          this.kc[indexg] = num.ball;
          this.dd = this.kc;
          this.cn = this.dd.join("");
        }
        let count = this.getCountFront(
          (this.an + "," + this.bn + "," + this.cn).split(","),
          3
        );
        this.$store.state.con =
          this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;
      }
      //组选胆拖、任选胆拖++
      if (this.className === "n11x5_x2_dt" ||this.className === "n11x5_star2_group_dt" ||this.className === "n11x5_x3_dt" ||this.className === "n11x5_star3_group_dt" ||this.className === "n11x5_x4_dt" ||this.className === "n11x5_x5_dt" ||this.className === "n11x5_x6_dt" ||this.className === "n11x5_x7_dt" ||this.className === "n11x5_x8_dt") {
        if (this.className === "n11x5_x2_dt" ||this.className === "n11x5_star2_group_dt") {
          this.dmNum = 1;
        }
        if (this.className === "n11x5_x3_dt" ||this.className === "n11x5_star3_group_dt") {
          this.dmNum = 2;
        }
        if (this.className === "n11x5_x4_dt") {
          this.dmNum = 3;
        }
        if (this.className === "n11x5_x5_dt") {
          this.dmNum = 4;
        }
        if (this.className === "n11x5_x6_dt") {
          this.dmNum = 5;
        }
        if (this.className === "n11x5_x7_dt") {
          this.dmNum = 6;
        }
        if (this.className === "n11x5_x8_dt") {
          this.dmNum = 7;
        }
        if (indexff === 0) {
          this.dmArr.push(num.ball);
          if (this.dmArr.length > 0) {
            if (this.dmArr.length > this.dmNum) {
              this.dmArr.splice(-2, 1);
              numViews.nums.forEach((item, index) => {
                item.choose = false;
              });
              this.dmArr.forEach(item => {
                let index = item[0] == "0" ? item[1] - 1 : item - 1;
                numViews.nums[index].choose = true;
              });
            }
          }
          //胆拖反选
          player.numView[1].nums.forEach(item => {
            if (player.numView[1].nums[indexg].choose === true &&num.choose === true) {
              player.numView[1].nums[indexg].choose = false;
              this.kb.splice(indexg, 1, "");
              this.dd = this.kb;
              this.bn = this.dd.join("");
              this.tm = this.strToarr(this.bn).join(",");
            }
          });
          this.ka = this.dmArr;
          this.dd = this.ka;
          this.an = this.dd.join("");
          this.dm = this.dd.join(",");
        }
        if (indexff === 1) {
          //胆拖反选
          player.numView[0].nums.forEach(item => {
            if (player.numView[0].nums[indexg].choose === true &&num.choose === true) {
              player.numView[0].nums[indexg].choose = false;
              this.dmArr.splice(this.dmArr.findIndex(item => item === num.ball),1);
              this.ka = this.dmArr;
              this.dd = this.ka;
              this.an = this.dd.join("");
              this.dm = this.dd.join(",");
            }
          });
          this.kb[indexg] = num.ball;
          this.dd = this.kb;
          this.bn = this.dd.join("");
          this.tm = this.strToarr(this.bn).join(",");
        }
        let count = this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
        this.$store.state.con = "胆" + this.dm + ";" + this.tm;
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;        
      }
    },
    //投注 ----
    bet_boxjian(indexff, indexg, num, numViews, player) {
      //任选复式、不定位
      if(this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1"){
        this.ka.splice(indexg, 1, "");
        this.dd = this.ka.filter(function(n) {return n;});
        this.an = this.dd.join(",");
        this.$store.state.con =this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选一定位胆 --
      if (this.className === "n11x5_dwd") {
        if (indexff === 0) {
          this.ka.splice(indexg, 1, "");
          this.dd = this.ka;
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb.splice(indexg, 1, "");
          this.dd = this.kb;
          this.bn = this.dd.join("");
        }
        if (indexff === 2) {
          this.kc.splice(indexg, 1, "");
          this.dd = this.kc;
          this.cn = this.dd.join("");
        }
        if (indexff === 3) {
          this.kd.splice(indexg, 1, "");
          this.dd = this.kd;
          this.dn = this.dd.join("");
        }
        if (indexff === 4) {
          this.ke.splice(indexg, 1, "");
          this.dd = this.ke;
          this.en = this.dd.join("");
        }
        if (this.an === "") {
          this.an = "-";
        }
        if (this.bn === "") {
          this.bn = "-";
        }
        if (this.cn === "") {
          this.cn = "-";
        }
        if (this.dn === "") {
          this.dn = "-";
        }
        if (this.en === "") {
          this.en = "-";
        }
        this.$store.state.con =
          this.an +
          "," +
          this.bn +
          "," +
          this.cn +
          "," +
          this.dn +
          "," +
          this.dn;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8") {
        this.ka.splice(indexg, 1, "");
        this.dd = this.ka.filter(function(n) {return n;});
        this.an = this.dd.join(",");
        this.fushi();
        this.$store.state.con =this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二直选复式 -
      if (this.className === "n11x5_star2_front") {
        if (indexff === 0) {
          this.ka.splice(indexg, 1, "");
          this.dd = this.ka;
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb.splice(indexg, 1, "");
          this.dd = this.kb;
          this.bn = this.dd.join("");
        }
        let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
        this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;
      }
      //选三直选复式 -
      if (this.className === "n11x5_star3_front") {
        if (indexff === 0) {
          this.ka.splice(indexg, 1, "");
          this.dd = this.ka;
          this.an = this.dd.join("");
        }
        if (indexff === 1) {
          this.kb.splice(indexg, 1, "");
          this.dd = this.kb;
          this.bn = this.dd.join("");
        }
        if (indexff === 2) {
          this.kc.splice(indexg, 1, "");
          this.dd = this.kc;
          this.cn = this.dd.join("");
        }
        let count = this.getCountFront(
          (this.an + "," + this.bn + "," + this.cn).split(","),
          3
        );
        this.$store.state.con =
          this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;
      }
      //任选胆拖 --
      //组选胆拖 --
      if (
        this.className === "n11x5_x2_dt" ||
        this.className === "n11x5_star2_group_dt" ||
        this.className === "n11x5_x3_dt" ||
        this.className === "n11x5_star3_group_dt" ||
        this.className === "n11x5_x4_dt" ||
        this.className === "n11x5_x5_dt" ||
        this.className === "n11x5_x6_dt" ||
        this.className === "n11x5_x7_dt" ||
        this.className === "n11x5_x8_dt"
      ) {
        if (indexff === 0) {
          this.dmArr.push(num.ball);
          if (this.dmArr.length > 0) {
            if (this.dmArr.length > this.dmNum) {
              this.dmArr.splice(-2, 1);
            }
          }
          this.ka = this.dmArr;
          this.dd = this.ka;
          this.an = this.dd.join("");
          this.dm = this.dd.join(",");
        }
        if (indexff === 1) {
          this.kb.splice(indexg, 1, "");
          this.dd = this.kb;
          this.bn = this.dd.join("");
          this.tm = this.strToarr(this.bn).join(",");
        }
        let count = this.getCountDt(
          (this.an + "," + this.bn).split(","),
          this.dmNum + 1
        );
        this.$store.state.con = "胆" + this.dm + ";" + this.tm;
        this.$store.state.pd.addCon = this.$store.state.con;
        this.$store.state.zhu = count;
        this.$store.state.pd.addzhu = this.$store.state.zhu;
      }
    },
    //----------公用函数-----------
    //排列组合
    groupSplit(arr, size) {
      let maxSize = arr.length,
        groupArr = [];
      if (size >= 1 && size <= maxSize) {
        getArr(arr, 0, []);
      }
      function each(arr, index, fn) {
        for (let i = index; i < maxSize; i++) {
          fn(arr[i], i, arr);
        }
      }
      function getArr(arr, _size, _arr, index) {
        if (_size === size) {
          return;
        }
        let len = _size + 1;
        each(arr, index || 0, function(val, i, arr) {
          _arr.splice(_size, 1, val);
          if (_size === size - 1) {
            groupArr.push(_arr.slice());
          }
          getArr(arr, len, _arr, i + 1);
        });
      }
      return groupArr;
    },
    //组选共用函数
    getCombin(num, len) {
      if (num < len) {
        return 0;
      }
      let nums = 1;
      let lens = 1;
      for (let i = 0; i < len; i++) {
        nums *= num - i;
        lens *= len - i;
      }
      return nums / lens;
    },
    //字符串"010203"拆分成数组["01","02","03"]
    strToarr(str) {
      return str.split("").reduce((arr, v, i, self) => {
        return !(i % 2)
          ? arr.concat(self.slice(i, i + 2).join(""))
          : arr.concat();
      }, []);
    },
    //胆拖
    getCountDt(betContent, stars) {
      let count = 0;
      let dmArr = [];
      let tmArr = [];
      for (let i = 0; i < betContent.length; i++) {
        let n = betContent[i];
        n = this.strToarr(n);
        if (i === 0) {
          dmArr = n;
        } else if (i === 1) {
          tmArr = n;
        }
      }
      if (dmArr.length === 0) {
        return 0;
      }
      count = this.groupSplit(tmArr, stars - 1).length;
      return count * dmArr.length;
    },
    //直选排列组合
    getCountFront(betContent, stars) {
      if (betContent.length != stars) {
        return 0;
      }
      let count = 0;
      let tempArr = [];
      for (let i = 0; i < stars; i++) {
        let n = betContent[i];
        n = this.strToarr(n);
        tempArr.push(n);
      }
      if (stars == 2) {
        tempArr[0].forEach((item1, index1) => {
          tempArr[1].forEach((item2, index2) => {
            if (item1 != item2) {
              count++;
            }
          });
        });
      } else if (stars == 3) {
        tempArr[0].forEach((item1, index1) => {
          tempArr[1].forEach((item2, index2) => {
            if (item1 != item2) {
              tempArr[2].forEach((item3, index3) => {
                if (item1 !== item2 && item1 !== item3 && item2 !== item3) {
                  count++;
                }
              });
            }
          });
        });
      }
      return count;
    },
    //菜单选择项1
    playGroupBut(item, index) {
      // this.$emit("clearTimeInters");//清除定时器
      this.playNum = 0;
      this.navTo = index;
      this.current_player = item;
      this.current_player_bonus = item.groups[0].players[0];
      this.className = this.current_player_bonus.id;
      this.$store.state.className = this.current_player_bonus.id;
      this.iscreat();
      switch (item.title) {
        case "选一":
          this.addTitle = "任选一任选复式";
          break;
        case "选二":
          this.addTitle = "任选二任选复式";
          break;
        case "选三":
          this.addTitle = "任选三任选复式";
          break;
        case "选四":
          this.addTitle = "任选四任选复式";
          break;
        case "选五":
          this.addTitle = "任选五任选复式";
          break;
        case "选六":
          this.addTitle = "任选六任选复式";
          break;
        case "选七":
          this.addTitle = "任选七任选复式";
          break;
        case "选八":
          this.addTitle = "任选八任选复式";
          break;
      }
    },
    //菜单选择项2
    playersBut(play, indexff, indexabc) {
      this.playNum = indexff;
      this.current_player_bonus = play;
      this.className = play.id;
      this.$store.state.className = play.id;
      this.addTitle = play.fullTitle;
      this.displayBonus = play.displayBonus;
      if (isNaN(this.displayBonus)) {
        let ar = [];
        ar = this.displayBonus.split("-");
        this.displayBonus1 = Number(ar[0]);
        this.displayBonus2 = Number(ar[1]);
        this.displayBonus3 = this.displayBonus1 + "-" + this.displayBonus2;
      }
      this.iscreat();
    },
    //清空
    iscreat() {
      this.$store.state.zhu = 0;
      this.$store.state.pd = {};
      // this.$store.state.spinner3 = 0;
      this.$store.state.con = "";
      this.d = [];
      this.dd = [];
      this.ka = [];
      this.kb = [];
      this.kc = [];
      this.kd = [];
      this.ke = [];
      this.dm = "";
      this.tm = "";
      this.dmNum = "";
      this.dmArr = [];
      this.an = "";
      this.bn = "";
      this.cn = "";
      this.dn = "";
      this.en = "";
      for (let h = 0; h < this.current_player_bonus.numView.length; h++) {
        for (
          let k = 0;
          k < this.current_player_bonus.numView[h].nums.length;
          k++
        ) {
          this.current_player_bonus.numView[h].nums[k].choose = false;
        }
      }
    },
    //胆拖--清
    dtiszhu6(indexf,ball,player){
      if(indexf === 1){
        ball.filter((list, i) => {
          list.choose = false;
          this.kb=[];
          this.tm = this.kb;
        });
        let dm = this.dm.split(",");
        if(dm.length > 0){
          dm.forEach((item,index) => {
            ball[index].choose = false;
            this.tm.splice(this.tm.indexOf(item),1,"");
            this.bn = this.tm.join("");
          });
        }
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //胆拖--全
    dtAll(indexf,ball, player){
      player.numView[0].nums.forEach(item => {
        player.numView[0].nums[indexf].choose = false;
        item.choose = false;
        this.dmArr= [];
        this.an = "";
        this.dm = "";
      });
      if(indexf === 1){
        ball.filter((list, i) => {
          list.choose = true;
          this.kb[i] = list.ball;
          this.tm = this.kb;
          this.bn = this.tm.join("");
        });
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //胆拖--大
    dtBig(indexf,ball, player){
      let leng = Math.floor(player.numView[0].nums.length / 2);
      let len = Math.floor(ball.length / 2);
      this.dmArr=[];
      player.numView[0].nums.forEach((item,index) => {
        if(index >= leng){
          item.choose = false;
        }
        if(item.choose){
          this.dmArr.push(item.ball);
        }
      });
      this.ka = this.dmArr;
      this.dd = this.ka;
      this.an = this.dd.join("");
      this.dm = this.dd.join(",");
      if(indexf === 1){
        ball.filter((list, i) => {
          if (i >= len) {
            list.choose = true;
            this.kb[i] = list.ball;
            this.tm = this.kb;
            this.bn = this.tm.join("");
          }
        });
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //胆拖--小
    dtSmall(indexf,ball, player){
      let leng = Math.floor(player.numView[0].nums.length / 2);
      let len = Math.floor(ball.length / 2);
      this.dmArr=[];
      player.numView[0].nums.forEach((item,index) => {
        if(index < leng){
          item.choose = false;
        }
        if(item.choose){
          this.dmArr.push(item.ball);
        }
      });
      this.ka = this.dmArr;
      this.dd = this.ka;
      this.an = this.dd.join("");
      this.dm = this.dd.join(",");
      if(indexf === 1){
        ball.filter((list, i) => {
          if (i < len) {
            list.choose = true;
            this.kb[i] = list.ball;
            this.tm = this.kb;
            this.bn = this.tm.join("");
          }
        });
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //胆拖--单
    dtOdd(indexf,ball, player){
      this.dmArr=[];
      player.numView[0].nums.forEach((item,index) => {
        if(item.ball % 2 === 1){
          item.choose = false;
        }
        if(item.choose){
          this.dmArr.push(item.ball);
        }
      });
      this.ka = this.dmArr;
      this.dd = this.ka;
      this.an = this.dd.join("");
      this.dm = this.dd.join(",");
      if(indexf === 1){
        ball.filter((list, i) => {
          if (list.ball % 2 === 1) {
            list.choose = true;
            this.kb[i] = list.ball;
            this.tm = this.kb;
            this.bn = this.tm.join("");
          }
        });
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //胆拖--双
    dtEven(indexf,ball, player){
      this.dmArr=[];
      player.numView[0].nums.forEach((item,index) => {
        if(item.ball % 2 === 0){
          item.choose = false;
        }
        if(item.choose){
          this.dmArr.push(item.ball);
        }
      });
      this.ka = this.dmArr;
      this.dd = this.ka;
      this.an = this.dd.join("");
      this.dm = this.dd.join(",");
      if(indexf === 1){
        ball.filter((list, i) => {
          if (list.ball % 2 === 0) {
            list.choose = true;
            this.kb[i] = list.ball;
            this.tm = this.kb;
            this.bn = this.tm.join("");
          }
        });
      }
      this.$store.state.con = "胆" + this.dm + ";" + this.tm.filter(function(n) {return n;});
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.zhu =this.getCountDt((this.an + "," + this.bn).split(","),this.dmNum + 1);
      this.$store.state.pd.addzhu = this.$store.state.zhu;
    },
    //全 ++++
    iszhu1({ ball }, item, indexf, player) {
      //选一 +
      if (this.className === "n11x5_x1"||this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if(this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1"){
          ball.filter((list, i) => {
            list.choose = true;
            this.ka[i] = list.ball;
            this.dd = this.ka.filter(function(n) {return n;});
            this.an = this.dd.join(",");
          });
          this.zhu1 = 11;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.con = this.an;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if(this.className === "n11x5_dwd"){
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join("");
            });
            this.zhu1 = 11;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kb[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.bn = this.dd.join("");
            });
            this.zhu2 = 11;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kc[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.cn = this.dd.join("");
            });
            this.zhu3 = 11;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kd[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.dn = this.dd.join("");
            });
            this.zhu4 = 11;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              list.choose = true;
              this.ke[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.en = this.dd.join("");
            });
            this.zhu5 = 11;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
          list.choose = true;
          this.ka[i] = list.ball;
          this.dd = this.ka.filter(function(n) {return n;});
          this.an = this.dd.join(",");
        });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join("");
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kb[i] = list.ball;
              this.dd = this.kb.filter(function(n) {return n;});
              this.bn = this.dd.join("");
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtAll(indexf,ball, player);
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选三直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join("");
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kb[i] = list.ball;
              this.dd = this.kb.filter(function(n) {return n;});
              this.bn = this.dd.join("");
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              list.choose = true;
              this.kc[i] = list.ball;
              this.dd = this.kc.filter(function(n) {return n;});
              this.cn = this.dd.join("");
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," +this.cn+ ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtAll(indexf,ball, player);
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtAll(indexf,ball, player);
      }
    },
    //大 ++++
    iszhu2({ ball }, item, indexf, player) {
      let len = Math.floor(ball.length / 2);
      //选一 +
      if (this.className === "n11x5_x1" || this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if (this.className === "n11x5_x1" ||this.className === "n11x5_front3_nx1") {
          ball.filter((list, i) => {
            if (i >= len) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
          this.zhu1 = 6;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.con = this.an;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if (this.className === "n11x5_dwd") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
            this.zhu1 = 6;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
            this.zhu2 = 6;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {return n;});
                this.cn = this.dd.join("");
              }
            });
            this.zhu3 = 6;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kd[i] = list.ball;
                this.dd = this.kd.filter(function(n) {return n;});
                this.dn = this.dd.join("");
              }
            });
            this.zhu4 = 6;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.ke[i] = list.ball;
                this.dd = this.ke.filter(function(n) {return n;});
                this.en = this.dd.join("");
              }
            });
            this.zhu5 = 6;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
          if (i >= len) {
            list.choose = true;
            this.ka[i] = list.ball;
            this.dd = this.ka.filter(function(n) {return n;});
            this.an = this.dd.join(",");
          }
        });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtBig(indexf,ball, player);
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选三直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (i >= len) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {return n;});
                this.cn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtBig(indexf,ball, player);
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtBig(indexf,ball, player);
      }
    },
    //小 ++++
    iszhu3({ ball }, item, indexf, player) {
      let len = Math.floor(ball.length / 2);
      //选一 +
      if (this.className === "n11x5_x1"||this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if (this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1") {
          ball.filter((list, i) => {
            if (i < len) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
          this.zhu1 = 5;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.con = this.an;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if (this.className === "n11x5_dwd") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
            this.zhu1 = 5;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
            this.zhu2 = 5;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {
                  return n;
                });
                this.cn = this.dd.join("");
              }
            });
            this.zhu3 = 5;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kd[i] = list.ball;
                this.dd = this.kd.filter(function(n) {
                  return n;
                });
                this.dn = this.dd.join("");
              }
            });
            this.zhu4 = 5;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.ke[i] = list.ball;
                this.dd = this.ke.filter(function(n) {
                  return n;
                });
                this.en = this.dd.join("");
              }
            });
            this.zhu5 = 5;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
            if (i < len) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtSmall(indexf,ball, player)
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选三直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (i < len) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {return n;});
                this.cn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtSmall(indexf,ball, player)
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtSmall(indexf,ball, player);
      }
    },
    //单 ++++
    iszhu4({ ball }, item, indexf, player) {
      let len = Math.ceil(ball.length / 2);
      //选一 +
      if (this.className === "n11x5_x1"||this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if (this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1") {
          ball.filter((list, i) => {
            if (list.ball % 2 === 1) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
          this.zhu1 = 6;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.con = this.an;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if (this.className === "n11x5_dwd") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {
                  return n;
                });
                this.an = this.dd.join("");
              }
            });
            this.zhu1 = 6;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {
                  return n;
                });
                this.bn = this.dd.join("");
              }
            });
            this.zhu2 = 6;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {
                  return n;
                });
                this.cn = this.dd.join("");
              }
            });
            this.zhu3 = 6;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kd[i] = list.ball;
                this.dd = this.kd.filter(function(n) {
                  return n;
                });
                this.dn = this.dd.join("");
              }
            });
            this.zhu4 = 6;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.ke[i] = list.ball;
                this.dd = this.ke.filter(function(n) {
                  return n;
                });
                this.en = this.dd.join("");
              }
            });
            this.zhu5 = 6;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
            if (list.ball % 2 === 1) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtOdd(indexf,ball, player);
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 1) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {return n;});
                this.cn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtOdd(indexf,ball, player);
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtOdd(indexf,ball, player);
      }
    },
    //双 ++++
    iszhu5({ ball }, item, indexf, player) {
      let len = Math.ceil(ball.length / 2);
      //选一 +
      if (this.className === "n11x5_x1"||this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if (this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1") {
          ball.filter((list, i) => {
            if (list.ball % 2 === 0) {
              list.choose = true;
              this.ka[i] = list.ball;
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join(",");
            }
          });
          this.zhu1 = 5;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.con = this.an;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if (this.className === "n11x5_dwd") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {
                  return n;
                });
                this.an = this.dd.join("");
              }
            });
            this.zhu1 = 5;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {
                  return n;
                });
                this.bn = this.dd.join("");
              }
            });
            this.zhu2 = 5;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {
                  return n;
                });
                this.cn = this.dd.join("");
              }
            });
            this.zhu3 = 5;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kd[i] = list.ball;
                this.dd = this.kd.filter(function(n) {
                  return n;
                });
                this.dn = this.dd.join("");
              }
            });
            this.zhu4 = 5;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.ke[i] = list.ball;
                this.dd = this.ke.filter(function(n) {
                  return n;
                });
                this.en = this.dd.join("");
              }
            });
            this.zhu5 = 5;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
          if (list.ball % 2 === 0) {
            list.choose = true;
            this.ka[i] = list.ball;
            this.dd = this.ka.filter(function(n) {return n;});
            this.an = this.dd.join(",");
          }
        });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtEven(indexf,ball, player);
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.ka[i] = list.ball;
                this.dd = this.ka.filter(function(n) {return n;});
                this.an = this.dd.join("");
              }
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kb[i] = list.ball;
                this.dd = this.kb.filter(function(n) {return n;});
                this.bn = this.dd.join("");
              }
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              if (list.ball % 2 === 0) {
                list.choose = true;
                this.kc[i] = list.ball;
                this.dd = this.kc.filter(function(n) {return n;});
                this.cn = this.dd.join("");
              }
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtEven(indexf,ball, player);
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtEven(indexf,ball, player);
      }
    },
    //清 ++++
    iszhu6({ ball }, item, indexf, player) {
      //选一 +
      if (this.className === "n11x5_x1"||this.className === "n11x5_dwd"||this.className === "n11x5_front3_nx1") {
        //任选复式、不定位
        if (this.className === "n11x5_x1"||this.className === "n11x5_front3_nx1") {
          ball.filter((list, i) => {
            list.choose = false;
            this.ka = [];
            this.dd = this.ka.filter(function(n) {return n;});
            this.an = this.dd.join(",");
          });
          this.zhu1 = 0;
          this.$store.state.con =this.an;
          this.$store.state.zhu = this.zhu1;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
          this.$store.state.pd.addCon = this.$store.state.con;
        }
        //定位胆
        if (this.className === "n11x5_dwd") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = false;
              this.ka = [];
              this.dd = this.ka.filter(function(n) {
                return n;
              });
              this.an = this.dd.join("");
            });
            this.zhu1 = 0;
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kb = [];
              this.dd = this.kb.filter(function(n) {
                return n;
              });
              this.bn = this.dd.join("");
            });
            this.zhu2 = 0;
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kc = [];
              this.dd = this.kc.filter(function(n) {
                return n;
              });
              this.cn = this.dd.join("");
            });
            this.zhu3 = 0;
          }
          if (indexf === 3) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kd = [];
              this.dd = this.kd.filter(function(n) {
                return n;
              });
              this.dn = this.dd.join("");
            });
            this.zhu4 = 0;
          }
          if (indexf === 4) {
            ball.filter((list, i) => {
              list.choose = false;
              this.ke = [];
              this.dd = this.ke.filter(function(n) {
                return n;
              });
              this.en = this.dd.join("");
            });
            this.zhu5 = 0;
          }
          if (this.an === "") {this.an = "-";this.zhu1=0;}
          if (this.bn === "") {this.bn = "-";this.zhu2=0;}
          if (this.cn === "") {this.cn = "-";this.zhu3=0;}
          if (this.dn === "") {this.dn = "-";this.zhu4=0;}
          if (this.en === "") {this.en = "-";this.zhu5=0;}
          this.$store.state.con =this.an +"," +this.bn +"," +this.cn +"," +this.dn +"," +this.en;
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu =(this.ka.filter(function(n) {return n;})).length + (this.kb.filter(function(n) {return n;})).length + (this.kc.filter(function(n) {return n;})).length + (this.kd.filter(function(n) {return n;})).length + (this.ke.filter(function(n) {return n;})).length;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
      }
      //选二，三，四，五，六，七，八（任选复式、组选复式）
      if (this.className === "n11x5_x2" ||this.className === "n11x5_star2_group" ||this.className === "n11x5_x3" ||this.className === "n11x5_star3_group" ||this.className === "n11x5_x4" ||this.className === "n11x5_x5" ||this.className === "n11x5_x6" ||this.className === "n11x5_x7" ||this.className === "n11x5_x8"){
        ball.filter((list, i) => {
          list.choose = false;
          this.ka = [];
          this.dd = this.ka.filter(function(n) {return n;});
          this.an = this.dd.join(",");
        });
        this.fushi();
        this.$store.state.con = this.an;
        this.$store.state.pd.addCon = this.$store.state.con;
      }
      //选二 +
      if (this.className === "n11x5_star2_front"||this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star2_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = false;
              this.ka = [];
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join("");
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kb = [];
              this.dd = this.kb.filter(function(n) {return n;});
              this.bn = this.dd.join("");
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn).split(","), 2);
          this.$store.state.con = this.an + "," + this.bn + ",-" + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x2_dt"||this.className === "n11x5_star2_group_dt"){
          this.dtiszhu6(indexf,ball,player);
        }
      }
      //选三 +
      if (this.className === "n11x5_star3_front"||this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
        //选二直选复式 ++
        if (this.className === "n11x5_star3_front") {
          if (indexf === 0) {
            ball.filter((list, i) => {
              list.choose = false;
              this.ka = [];
              this.dd = this.ka.filter(function(n) {return n;});
              this.an = this.dd.join("");
            });
          }
          if (indexf === 1) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kb = [];
              this.dd = this.kb.filter(function(n) {return n;});
              this.bn = this.dd.join("");
            });
          }
          if (indexf === 2) {
            ball.filter((list, i) => {
              list.choose = false;
              this.kc = [];
              this.dd = this.kc.filter(function(n) {return n;});
              this.cn = this.dd.join("");
            });
          }
          let count = this.getCountFront((this.an + "," + this.bn + "," + this.cn).split(","), 3);
          this.$store.state.con = this.an + "," + this.bn + "," + this.cn + ",-" + ",-";
          this.$store.state.pd.addCon = this.$store.state.con;
          this.$store.state.zhu = count;
          this.$store.state.pd.addzhu = this.$store.state.zhu;
        }
        if(this.className === "n11x5_x3_dt"||this.className === "n11x5_star3_group_dt"){
          this.dtiszhu6(indexf,ball);
        }
      }
      //选四、五、六、七、八胆拖 +
      if (this.className === "n11x5_x4_dt"||this.className === "n11x5_x5_dt"||this.className === "n11x5_x6_dt"||this.className === "n11x5_x7_dt"||this.className === "n11x5_x8_dt"){
        this.dtiszhu6(indexf,ball);
      }
    },
    aa() {
      this.dd = this.d.filter(function(n) {return n;});
      this.$store.state.pd.addTitle = this.addTitle;
      this.$store.state.con = this.dd.join(",");
      this.$store.state.pd.addCon = this.$store.state.con;
      this.$store.state.pd.addPattern = "元";
      this.$store.state.pd.addzhu = this.$store.state.zhu;
      this.$store.state.pd.addMoney = this.$store.state.spinner3;
      this.$store.state.pd.addClassName = this.$store.state.className;
      this.$store.state.pd.addSeasonId = this.$store.state.seasonId;
      this.$store.state.pd.addName = this.$store.state.lottName;
    },
    //全大小单双清
    toolsCur(tools, idx, item, indexff, player) {
      if (Object.is(tools.fncode, "full")) {
        this.full({ ball: item.nums }, item, indexff, player);
      } else if (Object.is(tools.fncode, "big")) {
        this.big({ ball: item.nums }, item, indexff, player);
      } else if (Object.is(tools.fncode, "small")) {
        this.small({ ball: item.nums }, item, indexff, player);
      } else if (Object.is(tools.fncode, "single")) {
        this.single({ ball: item.nums }, item, indexff, player);
      } else if (Object.is(tools.fncode, "double")) {
        this.double({ ball: item.nums }, item, indexff, player);
      } else {
        this.empty({ ball: item.nums }, item, indexff, player);
      }
    },
    //全
    full({ ball }, item, indexf, player) {
      this.empty({ ball }, item, indexf, player);
      this.aa();
      this.iszhu1({ ball }, item, indexf, player);
    },
    //大
    big({ ball }, item, indexf, player) {
      this.empty({ ball }, item, indexf, player);
      this.aa();
      this.iszhu2({ ball }, item, indexf, player);
    },
    //小
    small({ ball }, item, indexf, player) {
      this.empty({ ball }, item, indexf, player);
      this.aa();
      this.iszhu3({ ball }, item, indexf, player);
    },
    //单
    single({ ball }, item, indexf, player) {
      this.empty({ ball }, item, indexf, player);
      this.aa();
      this.iszhu4({ ball }, item, indexf, player);
    },
    //双
    double({ ball }, item, indexf, player) {
      this.empty({ ball }, item, indexf, player);
      this.aa();
      this.iszhu5({ ball }, item, indexf, player);
    },
    //清
    empty({ ball }, item, indexf, player) {
      this.aa();
      this.iszhu6({ ball }, item, indexf, player);
    },
    //排列组合
    groupSplit(arr, size) {
      let maxSize = arr.length,
        groupArr = [];
      if (size >= 1 && size <= maxSize) {
        getArr(arr, 0, []);
      }
      function each(arr, index, fn) {
        for (let i = index; i < maxSize; i++) {
          fn(arr[i], i, arr);
        }
      }
      function getArr(arr, _size, _arr, index) {
        if (_size === size) {
          return;
        }
        let len = _size + 1;
        each(arr, index || 0, function(val, i, arr) {
          _arr.splice(_size, 1, val);
          if (_size === size - 1) {
            groupArr.push(_arr.slice());
          }
          getArr(arr, len, _arr, i + 1);
        });
      }
      return groupArr;
    },
    //清空
    exit() {
      this.productList = [];
    }
  },
  filters: {
    keepTwoNum(value) {
      value = Number(value);
      return value.toFixed(2);
    }
  }
};
</script>
<style scoped lang="scss">
@import "@/assets/scss/lotterbet/lottlist.scss";
@import "@/assets/scss/lotterbet/ssc.scss";
</style>
