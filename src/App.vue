<template>
  <div id="app">
    <el-container style="padding: 20px;">
      <el-header><h2>题目管理系统</h2></el-header>
      <el-main>
        <!-- 题目列表组件 -->
        <QuestionList />
      </el-main>
    </el-container>
  </div>
</template>

<script>
import QuestionList from './components/QuestionList.vue'

export default {
  components: {
    QuestionList
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.el-header {
  background-color: #f5f7fa;
  text-align: center;
  line-height: 60px;
}
</style>