<template>
  <div id="app">
    <el-container>
      <el-header class="lf lf-j-b head">
        <span>微信集赞神器</span>
      </el-header>
      <el-main>
        <weZan />
      </el-main>
      <el-footer class="lf lf-a lf-j">
        <span>公众号：卖坚果的怪叔叔</span>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
import weZan from "./view/weZan.vue";

export default {
  name: "App",
  components: {
    weZan
  }
};
</script>

<style>
body {
  padding: 0;
  margin: 0;
}
</style>
<style scoped>
.el-header,
.el-footer {
  background-color: #686e89;
  color: #fff;
  text-align: center;
  line-height: 60px;
  font-size: 30px;
}
.el-main {
  padding: 20px 0;
  margin: 0;
}
</style>
