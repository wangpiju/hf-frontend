<template lang='jade'>
.helpcenter
  .helpcenterBox
    .help-left
      h2(:style='helpLeftH2Style') 帮助中心
      ul
        li(:style="helpLeftLiStyle(index)", v-for='(item,index) in helpNav', :key='index', @click='toNav($event,item,index)') {{item.name}}
    .help-right
      .help-right-content(v-for='(item,indexs) in helpNav', :key='indexs', v-if='navNum === indexs')
        h2 {{item.name}}
        div(v-show='indexs === 0')
          p 点击网站右上角＂用户注册＂按钮，就可以进入用户注册页面；
          p 按照提示，填写注册信息，即可完成注册；
          p 账号为6-20位字符，支持字母和数字，禁止以0开头；
          p 密码为6-16位字符，支持数字、字母、符号；
          p 注册完成后，系统将随机分配一张头像，进入＂我的账户＂＂个人信息＂可以修改头像和设置昵称；
        div(v-show='indexs === 1')
          p 购彩前需要先进行充值，账户有余额才可以进行购彩投注；
          p 充值完成后，可进入＂购彩大厅＂选择您喜欢的彩票进行投注；
          p 彩票投注页面，提供了＂玩法说明＂＂走势图＂，可以帮您更快了解＂玩法规则＂和＂开奖走势＂
          p 投注完成后，可以进入＂我的账户＂＂投注记录＂，查看您的投注明细，查看是否中奖；
          p 投注完成后，需等开奖后，才能查看是否中奖；
          p 投注成功后，系统将自动从您的账户余额扣除对应的投注金额，如果中奖，奖金会自动添加到您的账户余额；
          p 可以进入＂我的账户＂＂交易记录＂，查看您的账户交易明细；
        div(v-show='indexs === 2')
          p 账号登录后，点击网站右上角＂充值＂按钮，就可以进入充值页面；
          p 目前网站提供了＂银行转账＂＂快捷支付＂＂支付宝＂＂微信支付＂四项充值方式；
          p 如实填写充值信息，并提交充值订单，1-2分钟左右，刷新账户余额，即可到账；
          p 点击＂我的账户＂＂交易记录＂可查看每日的交易明细；
          p 大额充值建议使用＂银行转账＂；
        div(v-show='indexs === 3')
          p 中奖后如何提现？点击网站右上角＂提现＂按钮，就可以进入提现页面；
          p 选择您绑定的银行卡，填写您要提现的金额，并输入＂安全密码＂；
          p 提现订单提交后，3-5分钟左右即可到账，可登陆银行查看银行账户余额；
          p 点击＂我的账户＂＂交易记录＂可查看每日的交易明细；
          p 提现之前需先设置＂安全密码＂和绑定您要用来提现的银行卡；
          p 提现金额不能大于可提现金额；
          p 为了提供更好的服务体验，降低广大用户提现订单的处理时间，每日最多只允许提现5次，建议用户合理安排每日的提现时间和次数；
        div(v-show='indexs === 4')
          p 直接在手机浏览器输入网址，即可登录手机版，无需下载；
          p 手机版随时随地可以进行投注，更加便捷，建议广大用户使用手机版进行投注；
          p 建议使用＂谷歌＂＂苹果＂浏览器，操作体验更佳；
        div(v-show='indexs === 5')
          p 在线客服提供全天24小时咨询服务，全年无休；
          p 在游戏中遇到任何问题，欢迎随时咨询我们的在线客服，我们竭诚为您提供最高品质的咨询服务；
        div(v-show='indexs === 6')
          p 安全密码不同于登陆密码，是另外要设置的一个密码；
          p 安全密码一般用于提现、绑定银行卡等操作，可以保障账户资金安全；
          p 为了账户安全，设置安全密码时不能跟登陆密码相同；
        div(v-show='indexs === 7')
          p 为了提供更好的服务体验，降低广大用户提现订单的处理时间，每日最多只允许提现5次，建议用户合理安排每日的提现时间和次数；
        div(v-show='indexs === 8')
          p 进入＂我的账户＂＂银行卡管理＂可以绑定您要用来提现的银行卡；
          p 绑定银行卡前，需先设置安全密码；
          p 银行卡最多只允许绑定5张；
          p 银行卡绑定支持所有主流银行；
          p 为了用户资金安全，已成功提现的银行卡将自动锁定，无法删除和修改；如想弃用银行卡，可联系在线客服禁用该银行卡；
        div(v-show='indexs === 9')
          p 为了广大用户的账户安全，网站的＂安全中心＂提供了多项提升账户安全系数的功能；
          p 新用户注册完成后，请及时进入＂安全中心＂绑定＂密保手机＂＂密保问题＂＂密保邮箱＂；
          p 用户忘记密码时，可通过以上功能，找回密码；
        div(v-show='indexs === 10')
          p 可提现金额，就是账户可以用来提现的金额，
          p 为防止洗钱，充值的钱，要进行投注，才可以提现，
          h3 可提现金额计算公式如下：
          p 可提现金额=投注金额+中奖金额（小于或等于余额）
          p *投注金额不含撤单的投注金额
</template>
<script>
export default {
  data() {
    return {
      GSN: JSON.parse(localStorage.getItem("globalstyle")),
      navNum:0,
      helpNav:[
        {name:'如何注册',isshow:false},
        {name:'如何购彩',isshow:false},
        {name:'如何充值',isshow:false},
        {name:'如何提现',isshow:false},
        {name:'手机如何购彩',isshow:false},
        {name:'在线客服时间',isshow:false},
        {name:'什么是安全码',isshow:false},
        {name:'每日提现次数',isshow:false},
        {name:'如何绑定银行卡',isshow:false},
        {name:'账户安全',isshow:false},
        {name:'什么是可提现金额',isshow:false},
      ]
    };
  },
  computed:{
    helpLeftH2Style(){
      return this.GSN.id=="image"?{ backgroundImage:"url("+this.GSN.color_boss_bg+")" } : { background: this.GSN.color_boss };
    },
  },
  methods:{
    helpLeftLiStyle(index){
      if(this.GSN.id !== "image" && this.navNum == index){
        return {color: this.GSN.color_hover,borderLeft:"3px solid "+ this.GSN.color_hover}
      }else if(this.GSN.id == "image" && this.navNum == index){
        return {color:'undefined',borderLeft:"undefined"}
      }
      
    },
    toNav(e,item,index){
      this.navNum = index;
    }
  }
};
</script>
<style lang="scss" scoped>
@import "../../assets/scss/helpcenter.scss";
</style>
