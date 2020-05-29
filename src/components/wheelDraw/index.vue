<template>
  <div class="wheel-container" :style="{
    width: width + 'px',
    height: width + 'px'
  }">
    <div
      class="wheel-list"
      :style="{
        background: 'url(' + bgImg + ') 0% 0% / 100% 100% no-repeat',
        transform: 'rotate(' + (degValue || initDeg) + 'deg)'
      }"
    >
      <div class="wheel-item" v-for="(item, index) in prizeList" :key="index">
        <div
          class="wheel-img"
          :style="{
            width: prizeWidthData,
            paddingTop: prizePaddingTopData,
            transform: 'rotate(' + (index + 0.5)/6 + 'turn)',
            transformOrigin: itemTransformOrigin,
          }"
        >
          <img :src="item.img" style="width: 100%;height:auto;">
        </div>
      </div>
    </div>
    <div class="wheel-btn">
      <img :src="btnImg" class="img" @click="start">
    </div>
  </div>
</template>
<script>
export default {
  name: "WheelDraw",
  props: {
    width: {
      default: 300
    },
    initDeg: {
      type: Number,
      default: 0
    },
    rotTimes: {
      type: Number,
      default: 1
    },
    prizeList: {
      type: Array,
      default: () => []
    },
    prizeName: {
      type: String,
      default: ""
    },
    prizeWidth: {
      type: Number,
      default: NaN
    },
    prizePaddingTop: {
      type: Number,
      defult: NaN
    },
    bgImg: {
      type: String,
      default:
        "https://gw.alipayobjects.com/zos/rmsportal/YIunNQVWkFRxUTaUNhOZ.png"
    },
    btnImg: {
      type: String,
      default:
        "https://gw.alipayobjects.com/zos/rmsportal/JHenAywYHZTLbbrnkIFN.png"
    },
    onStart: {
      type: Function,
      default: () => {}
    },
    onFinish: {
      type: Function,
      default: () => {}
    },
    onTimesUp: {
      type: Function,
      default: () => {}
    }
  },
  data() {
    return {
      count: 0,
      rotNum: 0,
      onRunning: false,
      degValue: 0,
      prizeWidthData: 0,
      prizePaddingTopData: 0,
      itemTransformOrigin: ""
    };
  },
  computed: {},
  mounted() {
    const widthNum = this._getNum(this.width);
    const widthUnit = this._getUnit(this.width);
    const prizeWidth = this.prizeWidth;
    const paddingTop = this.prizePaddingTop;
    this.degValue = this.initDeg;
    this.itemTransformOrigin = "50%" + 0.5 * widthNum + widthUnit;
    this.prizeWidthData = isNaN(prizeWidth)
      ? this._calculatePrizeWidth()
      : prizeWidth;
    this.prizePaddingTopData = isNaN(paddingTop)
      ? this._calculatePrizePaddingTop()
      : paddingTop;
    this.count = 6;
  },
  methods: {
    getIndexByName(name) {
      const list = this.prizeList;
      for (let i = 0; i < this.count; i++) {
        if (list[i] && list[i].name === name) return i;
      }
      return -1;
    },
    start() {
      if (this.onRunning) return;
      if (this.rotNum >= this.rotTimes) {
        this.onTimesUp();
        return;
      }
      if (!this.prizeName) {
        throw new Error("请传入抽奖结果名称：prizeName");
      }
      const index = this.getIndexByName(this.prizeName);
      if (index === -1) {
        throw new Error(
          "抽奖结果名称与抽奖列表配置项不匹配，未找到名称为" +
            this.prizeName +
            "的奖项"
        );
      }
      this.rotNum += 1;
      this.onRunning = true;
      const degree = (index + (index + 1)) * (360 / (this.count * 2));
      const degValue = 360 * this.count * this.rotNum - degree;
      this.degValue = degValue;
      this.onStart(this.prizeName, this.rotNum);
      setTimeout(() => {
        this.done();
      }, 6000);
    },
    done() {
      this.onRunning = false;
      this.onFinish(this.prizeName, this.rotNum);
    },
    _getNum(s) {
      return parseFloat(s);
    },
    _getUnit(s) {
      s += "";
      return (s.match(/[a-z]+$/) || [])[0] || "px";
    },
    _calculatePrizeWidth() {
      const widthNum = this._getNum(this.width);
      const widthUnit = this._getUnit(this.width);
      return (4 - 2 * Math.sqrt(3)) * 0.5 * widthNum + widthUnit;
    },
    _calculatePrizePaddingTop() {
      const widthNum = this._getNum(this.width);
      const widthUnit = this._getUnit(this.width);
      return 0.5 * widthNum - 0.25 * widthNum * Math.sqrt(3) + widthUnit;
    }
  }
};
</script>
<style scoped>
.wheel-container {
  position: relative;
  margin: auto;
  border-radius: 50%;
  overflow: hidden;
  height: 500px;
}
.wheel-list {
  position: absolute;
  border-radius: 50%;
  width: inherit;
  height: inherit;
  transition: all 6s ease;
}

.wheel-item {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}

.wheel-img {
  position: relative;
  display: block;
  margin: 0 auto;
  text-align: center;
}

.wheel-img img {
  display: block;
  width: 100%;
  height: 100%;
}

.wheel-btn {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.wheel-btn .img {
  width: 30%;
}
</style>

