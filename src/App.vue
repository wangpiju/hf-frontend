<template lang="jade">
#app
  router-view
</template>
<script>
export default {
  name: "App",
};
</script>
<style lang="scss">
@import "assets/minix/reset.scss";
@import "assets/minix/main.scss";
.gm-scrollbar.-vertical{
  background-color: rgb(237, 99, 93);
}
.gm-scrollbar.-horizontal{
  height: 0px;
}
.gm-scrollbar.-vertical .thumb{
  background-color: rgb(175, 58, 49);
}
</style>
