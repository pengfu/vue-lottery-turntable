<template>
  <div class="banner">
    <div class="turnplate">
      <canvas class="item" id="wheelcanvas" width="422px" height="422px"></canvas>
      <img id="pointer" class="pointer" :src="imgSrc" @click="startGame"/>
    </div>
    <img :src="shanImg" id="shan-img" style="display:none;"/>
    <img :src="sorryImg" id="sorry-img" style="display:none;"/>
  </div>
</template>
<style>
  .banner {
    display: block;
    max-width: 800px;
    margin: 0 auto;
    height: 100%;
  }

  .banner .turnplate {
    display: block;
    width: 100%;
    height: auto;
    position: relative;
    margin: 0 auto;
    background-image: url('../../assets/turnplate-bg.png');
    background-size: 100% auto;
    background-repeat: no-repeat;
  }

  .banner .turnplate img.pointer {
    position: absolute;
    width: 31.5%;
    height: 42.5%;
    left: 34.6%;
    top: 23%;
  }

  .banner .turnplate canvas.item {
    width: 100%;
  }

</style>
<script>
  import LotteryDial from './dial'
  import imgSrc from '../../assets/turnplate-pointer.png'
  import shanImg from '../../assets/1.png'
  import sorryImg from '../../assets/2.png'

  export default{
    data(){
      return {
        imgSrc,
        shanImg,
        sorryImg,
        lottery: null,
        turnplate: {
          outsideRadius: 192,			//大转盘外圆的半径
          textRadius: 155,				//大转盘奖品位置距离圆心的距离
          insideRadius: 68,			//大转盘内圆的半径
          startAngle: 1.5 * Math.PI,				//开始角度
        },
      }
    },

    props: {
      restaraunts:{
          type:Array,
          default:[]
      },
      colors:Array
    },

    computed: {
      oldrestaraunts() {
          return this.restaraunts.reverse()
      }
    },

    mounted() {
      this.loadImgs(() => {
        this.drawRouletteWheel()
        this.prepareLottery()
      })

    },

    methods: {
      drawRouletteWheel() {
        var canvas = document.getElementById("wheelcanvas");
        var turnplate = this.turnplate
        if (!canvas.getContext) {
          return
        }
        //根据奖品个数计算圆周角度
        var arc = Math.PI / (this.restaraunts.length / 2);
        var ctx = canvas.getContext("2d");
        //在给定矩形内清空一个矩形
        ctx.clearRect(0, 0, 422, 422);
        //strokeStyle 属性设置或返回用于笔触的颜色、渐变或模式
        ctx.strokeStyle = "#FFBE04";
        //font 属性设置或返回画布上文本内容的当前字体属性
        ctx.font = '16px Microsoft YaHei';
        for (var i = 0; i < this.restaraunts.length; i++) {
          var angle = turnplate.startAngle + i * arc;
          ctx.fillStyle = this.colors[i];
          ctx.beginPath();
          //arc(x,y,r,起始角,结束角,绘制方向) 方法创建弧/曲线（用于创建圆或部分圆）
          ctx.arc(211, 211, turnplate.outsideRadius, angle, angle + arc, false);
          ctx.arc(211, 211, turnplate.insideRadius, angle + arc, angle, true);
          ctx.stroke();
          ctx.fill();
          //锁画布(为了保存之前的画布状态)
          ctx.save();

          //----绘制奖品开始----
          ctx.fillStyle = "#E5302F";
          var text = this.restaraunts[i];
          var line_height = 17;
          //translate方法重新映射画布上的 (0,0) 位置
          ctx.translate(211 + Math.cos(angle + arc / 2) * turnplate.textRadius, 211 + Math.sin(angle + arc / 2) * turnplate.textRadius);

          //rotate方法旋转当前的绘图
          ctx.rotate(angle + arc / 2 + Math.PI / 2);

          /** 下面代码根据奖品类型、奖品名称长度渲染不同效果，如字体、颜色、图片效果。(具体根据实际情况改变) **/
          if (text.indexOf("M") > 0) {//流量包
            var texts = text.split("M");
            for (var j = 0; j < texts.length; j++) {
              ctx.font = j == 0 ? 'bold 20px Microsoft YaHei' : '16px Microsoft YaHei';
              if (j == 0) {
                ctx.fillText(texts[j] + "M", -ctx.measureText(texts[j] + "M").width / 2, j * line_height);
              } else {
                ctx.fillText(texts[j], -ctx.measureText(texts[j]).width / 2, j * line_height);
              }
            }
          } else if (text.indexOf("M") == -1 && text.length > 6) {//奖品名称长度超过一定范围
            text = text.substring(0, 6) + "||" + text.substring(6);
            var texts = text.split("||");
            for (var j = 0; j < texts.length; j++) {
              ctx.fillText(texts[j], -ctx.measureText(texts[j]).width / 2, j * line_height);
            }
          } else {
            //在画布上绘制填色的文本。文本的默认颜色是黑色
            //measureText()方法返回包含一个对象，该对象包含以像素计的指定字体宽度
            ctx.fillText(text, -ctx.measureText(text).width / 2, 0);
          }

          //添加对应图标
          if (text.indexOf("闪币") > 0) {
            var img = document.getElementById("shan-img");
            ctx.drawImage(img, -15, 10);
          } else if (text.indexOf("谢谢参与") >= 0) {
            var img = document.getElementById("sorry-img");
            ctx.drawImage(img, -15, 10);
          }
          //把当前画布返回（调整）到上一个save()状态之前
          ctx.restore();
          //----绘制奖品结束----
        }

      },
      prepareLottery() {
        var turnplate = this.turnplate
        var lottery = new LotteryDial(document.getElementById('wheelcanvas'), { // eslint-disable-line
          speed: 30, // 每帧速度
          areaNumber: this.restaraunts.length // 奖区数量
        })
        var index = -1

        lottery.on('start', () => {
          // 请求获取中奖结果
          index = Math.round(Math.random() * (this.restaraunts.length - 1))
          lottery.setResult(index)
        })

        lottery.on('end', () => {
          console.log('中奖奖区：' + index)
          alert(this.oldrestaraunts[index])
        })
        this.lottery = lottery
      },
      startGame(){
        this.lottery.draw()
      },
      loadImgs(suc) {
        var c = 0
        var images = [document.getElementById('shan-img'), document.getElementById('sorry-img')]
        for (var i = 0, len = images.length; i < len; i++) {
          (function loadImage(image) {
            image.onload = function () {
              c++
              c === images.length && suc && suc()
            }
          })(images[i])
        }
      }
    },
    components: {}
  }
</script>
