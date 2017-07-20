# vue-lottery-turntable

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

## How to Use
```javascript
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
```

## Demo
[![Demo](http://s.yunkexiongdi.com/o_1blf0s4461tbb1q9b1a0b1tjg14rn7.gif "Demo")](http://s.yunkexiongdi.com/o_1blf0s4461tbb1q9b1a0b1tjg14rn7.gif "Demo")
