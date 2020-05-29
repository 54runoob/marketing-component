<template>
  <div id="app">
    <WheelDraw
      :width="'300'"
      :prizeList="prizeList"
      :prizeName="prizeName"
      :rotTimes="totalTimes"
      :onStart="onStart"
      :onFinish="onFinish"
      :onTimesUp="onTimesUp"
    />
    <div class="times">
      <p>你还有{{totalTimes - curTimes}}次抽奖机会</p>
    </div>
    <div class="result">
      <p>{{result}}</p>
    </div>
  </div>
</template>

<script>
import WheelDraw from "./components/wheelDraw";

export default {
  name: "App",
  components: {
    WheelDraw
  },
  data() {
    return {
      prizeList: [],
      prizeName: "",
      totalTimes: 0,
      curTimes: 0,
      result: ""
    };
  },
  methods: {
    onStart(name, times) {
      // 转盘开始转动
      this.result = `第${times}次抽奖中，请稍候...`;
      this.curTimes = times++;
    },
    /*
    @param name 获奖项名字
    @param times 当前转动次数
  */
    // eslint-disable-next-line no-unused-vars
    onFinish(name, times) {
      // 转盘结束转动
      this.result =
        name === "未中奖" ? "很遗憾，差点就中奖了" : `恭喜你，获得${name}`;
      this.getNext().then(data => {
        if (data.code === 0) {
          this.prizeName = this.prizeList[data.data.random].name;
        }
      });
    },
    onTimesUp() {},
    init() {
      // eslint-disable-next-line no-unused-vars
      return new Promise((resolve, reject) => {
        resolve({
          code: 0,
          data: {
            totalTimes: 10,
            prizeList: [
              {
                name: "H&M100元优惠券",
                img:
                  "https://gw.alipayobjects.com/zos/rmsportal/nIQUKeYBbJWliGJVhVmx.png"
              },
              {
                name: "2元话费券",
                img:
                  "https://gw.alipayobjects.com/zos/rmsportal/HkrVjjjuxZPUMCUbPazb.png"
              },
              {
                name: "45元飞猪出行券",
                img:
                  "https://gw.alipayobjects.com/zos/rmsportal/cDctUxwBLPCszQHRapYV.png"
              },
              {
                name: "H&M10元优惠券",
                img:
                  "https://gw.alipayobjects.com/zos/rmsportal/FAmIWZAWpUwlRFKqQDLz.png"
              },
              {
                name: "2元流量券",
                img:
                  "https://gw.alipayobjects.com/zos/rmsportal/cuGomeXzMyeeZMjvVjBj.png"
              },
              {
                name: "未中奖",
                img:
                  "https://zos.alipayobjects.com/rmsportal/dwhgPyWAcXuvJAWlSSgU.png"
              }
            ],
            prizeName: "2元流量券"
          }
        });
      });
    },
    getNext() {
      // eslint-disable-next-line no-unused-vars
      return new Promise((resolve, reject) => {
        resolve({
          code: 0,
          data: {
            random: Math.floor(Math.random() * 6)
          }
        });
      });
    }
  },
  created() {
    setTimeout(() => {
      this.init().then(data => {
        if (data.code === 0) {
          this.totalTimes = data.data.totalTimes;
          this.prizeList = data.data.prizeList;
          this.prizeName = data.data.prizeName;
        }
      });
    }, 10);
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
