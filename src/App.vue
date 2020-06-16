<template>
  <div id="app">
    <el-container>
      <el-header class="lf lf-j-b">
        <span>demo</span>
      </el-header>
      <el-main>
        <home />
      </el-main>
      <el-footer class="lf lf-a lf-j">
        <span>demo-demo-demo</span>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
import Home from "./components/Home.vue";

export default {
  name: "App",
  components: {
    Home
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
}
.el-main {
  padding: 20px 0;
  margin: 0;
}
</style>
