<template>
  <el-card class="box-card">
    <div class="clearfix">
      <!-- @tab-click="handleClick" -->
      <!-- 头部左侧内容 -->
      <el-tabs class="tabs" v-model="activeName">
        <el-tab-pane label="销售额" name="sale"></el-tab-pane>
        <el-tab-pane label="访问量" name="visit"></el-tab-pane>
      </el-tabs>
      <!-- 头部有测内容 -->
      <div class="right">
        <span @click="setDay">今日</span>
        <span @click="setWeek">本周</span>
        <span @click="setMonth">本月</span>
        <span @click="setYear">本年</span>
        <!-- v-model="value1" -->
        <el-date-picker
          class="data"
          type="daterange"
          range-separator="-"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          size="mini"
          v-model="data"
          value-format="yyyy-MM-dd"
        >
        </el-date-picker>
      </div>
    </div>
    <!-- 主体区域 -->
    <div class="content">
      <el-row :gutter="10">
        <el-col :span="18">
          <div class="echart" ref="echart"></div>
        </el-col>
        <el-col :span="6">
          <div style="margin-top: 20px; font-size: 16px">
            门店{{ title }}排名
          </div>
          <ul>
            <li>
              <span class="rIndex">1</span><span>肯德基</span
              ><span>323,234</span>
            </li>
            <li>
              <span class="rIndex">2</span><span>麦当劳</span
              ><span>399,132</span>
            </li>
            <li>
              <span class="rIndex">3</span><span>肯德基</span
              ><span>283,234</span>
            </li>
            <li><span>4</span><span>海底捞</span><span>283,432</span></li>
            <li><span>5</span><span>西贝筱面村</span><span>334,784</span></li>
            <li><span>6</span><span>汉堡王</span><span>324,234</span></li>
            <li><span>7</span><span>真功夫</span><span>546,234</span></li>
          </ul>
        </el-col>
      </el-row>
    </div>
  </el-card>
</template>

<script>
import echarts from "echarts";
import dayjs from "dayjs";
export default {
  name: "Sale",
  data() {
    return {
      activeName: "sale",
      myEhart: null,
      data: [],
    };
  },
  mounted() {
    // 初始化echarts实例
    this.myEhart = echarts.init(this.$refs.echart);
    // 配置数据
    this.myEhart.setOption({
      title: {
        text: "销售额趋势",
        top: "20px",
      },
      tooltip: {
        trigger: "axis",
        axisPointer: {
          type: "shadow",
        },
      },
      grid: {
        left: "3%",
        right: "4%",
        bottom: "3%",
        containLabel: true,
      },
      xAxis: [
        {
          type: "category",
          data: [
            "1月",
            "2月",
            "3月",
            "4月",
            "5月",
            "6月",
            "7月",
            "8月",
            "9月",
            "10月",
            "11月",
            "12月",
          ],
          axisTick: {
            alignWithLabel: true,
          },
        },
      ],
      yAxis: [
        {
          type: "value",
        },
      ],
      series: [
        {
          name: "Direct",
          type: "bar",
          barWidth: "60%",
          data: [10, 52, 200, 334, 390, 330, 220, 34, 356, 123, 99, 231],
          color: "blue",
        },
      ],
    });
  },
  computed: {
    title() {
      return this.activeName == "sale" ? "销售" : "访问";
    },
  },
  //   监听属性
  watch: {
    title() {
      //   重新修改图标的配置数据
      // 图标配置数据可以再次修改，如果有新的数值，新的数值，没有新的数值，还是用以前的
      this.myEhart.setOption({
        title: {
          text: `${this.title}额趋势`,
        },
      });
    },
  },
  methods: {
    // 今日
    setDay() {
      let data = dayjs().format("YYYY-MM-DD");
      this.data = [data, data];
    },
    // 本周
    setWeek() {
      let start = dayjs().startOf("week").format("YYYY-MM-DD");
      let end = dayjs().endOf("week").format("YYYY-MM-DD");
      this.data = [start, end];
    },
    // 本月
    setMonth() {
      let start = dayjs().startOf("month").format("YYYY-MM-DD");
      let end = dayjs().endOf("month").format("YYYY-MM-DD");
      this.data = [start, end];
    },
    // 本年
    setYear() {
      let start = dayjs().startOf("year").format("YYYY-MM-DD");
      let end = dayjs().endOf("year").format("YYYY-MM-DD");
      this.data = [start, end];
    },
  },
};
</script>

<style scoped>
.box-card {
  margin: 15px 0;
}
.clearfix {
  position: relative;
}
.tabs {
  position: absolute;
  left: 0;
  width: 100%;
}
.right {
  position: absolute;
  right: 0;
}
.data {
  width: 250px;
}
.right span {
  margin: 0 10px;
}
.right span:nth-child(4) {
  margin-right: 20px;
}
.content {
  margin-top: 30px;
}
.echart {
  width: 100%;
  height: 300px;
}
ul {
  width: 100%;
  height: "280px";
  list-style: none;
}
ul li {
  width: 100%;
  height: "40px";
  font-size: 14px;
  margin: 10px 0;
}
ul li span:nth-child(2) {
  padding-left: 30px;
}
ul li span:nth-child(3) {
  float: right;
}
ul li .rIndex {
  float: left;
  width: 20px;
  height: 20px;
  background-color: black;
  color: white;
  padding: 4px;
  border-radius: 50%;
}
</style>