<template lang='jade'>
<!-- 今日开奖 -->
div
  .lott-right-top1
    button(:style='GSN.id == "image" ? { backgroundImage: "url(" + GSN.color_btn3_bg + ")" } : { background:GSN.color_btn3 }') 今日开奖
    p
      router-link(tag='a', target='_blank', :to='"/trendChart/1804/"+$route.params.group') 走势图
      i |
      // <span>玩法说明</span>
  .lott-right-top2
    p.lott-right-top2-title
      span.lott-right-top2-span1 期号
      span.lott-right-top2-span2.ssc 开奖号码
      span.lott-right-top2-span3.ssc 开奖时间
    ul
      li(v-for='(item,index) in getPastOpens', :key='index')
        span.lott-right-top2-span1 {{item.seasonId}}
        span.lott-right-top2-span2.ssc
          i {{item.n1 < 10 ? "0"+item.n1 : item.n1}}
          i {{item.n2 < 10 ? "0"+item.n2 : item.n2}}
          i {{item.n3 < 10 ? "0"+item.n3 : item.n3}}
          i {{item.n4 < 10 ? "0"+item.n4 : item.n4}}
          i {{item.n5 < 10 ? "0"+item.n5 : item.n5}}
        span.lott-right-top2-span3.ssc {{item.addTime.substring(11)}}
</template>
<script>
import { baseUrl } from "../../../assets/js/env";
export default {
  data(){
    return{
      GSN: JSON.parse(localStorage.getItem("globalstyle")),
    }
  },
  computed: {
    getPastOpens() {
      return this.$store.state.getPastOpens;
    }
  }
};
</script>
<style lang='scss' scoped>
@import "@/assets/scss/lotterbet/lottlist.scss";
</style>
