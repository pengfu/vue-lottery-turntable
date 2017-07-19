<template>
  <div id="app">
   <lottery-turntable :restaraunts="rests" :colors="colors"></lottery-turntable>
  </div>
</template>

<script>
import LotteryTurntable from './components/lottery/LotteryTurntable'

export default {
  name: 'app',
  data() {
    return {
      //奖品类别
      rests:["别墅一套", "奔驰一辆", "谢谢参与", "套套一箱", "自行车一辆", "苹果手机", "现金一万"],
      //大转盘奖品区块对应背景颜色
      colors: ["#FF0000", "#FF8000", "#FFFF00", "#00FF00", "#00FFFF", "#0000FF","#8000FF"]
    }
  },
  components: {
    LotteryTurntable
  }
}
</script>

<style>
  html,body{
    height: 100%;
  }
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  height: 100%;
}
</style>
