<template lang='jade'>
.rechargeBox
  .leftListArea
    ul.sildeSession(v-for='parent in parents',v-if='parent.choose === true')
      h3 
        i.iconfont(:class="parent.icon",style='margin:0 10px;') 
        span {{parent.item}}
      li(v-for='(son,index) in parent.son') 
        router-link(:to="son.to") {{son.name}}
  router-view
</template>
<script>
export default {
  data() {
    return {
      parents: [
        {
          item: "账户管理",
          choose:true,
          icon: "icon-user1",
          son: [
            { name: "个人信息", to: "/user/userinfo" },
            { name: "安全中心", to: "/user/securityCenter" },
            { name: "银行卡管理", to: "/user/manageBankcard" }
          ]
        },
        {
          item: "投注管理",
          choose:true,
          icon: "icon-users",
          son: [
            { name: "投注记录", to: "/betManage/betRecord" },
            // { name: "追号记录", to: "/betManage/seekOrder" }
          ]
        },
        {
          item: "资金管理",
          choose:true,
          icon: "icon-money",
          son: [
            { name: "交易记录", to: "/user/billRecord" },
            { name: "今日盈亏", to: "/user/PLstatement" }
          ]
        },
        {
          item: "代理中心",
          choose:false,
          icon: "icon-user1",
          son: [
            { name: "代理说明", to: "/agent/agentIntro" },
            { name: "代理报表", to: "/agent/agentReport" },
            { name: "下级报表", to: "/agent/lowerReport" },
            { name: "下级开户", to: "/agent/manageInvite" },
            { name: "会员管理", to: "/agent/agentMember" },
            { name: "投注明细", to: "/agent/agentBetRecord" },
            { name: "交易明细", to: "/agent/agentBillRecord" }
          ]
        },
        {
          item: "消息管理",
          choose:true,
          icon: "icon-mail-fill",
          son: [
            { name: "站内信", to: "/user/letter" },
            { name: "网站公告", to: "/user/notice" }
          ]
        }
      ]
    };
  },
  mounted(){
    this.noMember();
  },
  methods:{
    //非会员不显示代理
    noMember(){
      this.parents.forEach(el => {
        if(this.$store.state.userType === '0'){
          if(el.item === '代理中心'){
            el.choose = false;
          }
        }else if(this.$store.state.userType === '1'){
          if(el.item === '代理中心'){
            el.choose = true;
          }
        }
      });
    },
    getNoReadNoticeStatus(){
      this.$parent.getNoReadNoticeStatus();
    }
  }
};
</script>
<style lang="scss" scoped>
@import "../../assets/scss/user.scss";
</style>
