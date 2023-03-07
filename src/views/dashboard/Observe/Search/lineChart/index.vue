<template>
  <div class="chart">
    <div class="chart-header">
      <span class="search">搜索用户数</span>
      <i class="el-icon-info"></i>
    </div>
    <div class="chart-content">
      <span>12321</span><span>17.1</span>
      <i class="el-icon-caret-top" style="color: red"></i>
      <i class="el-icon-caret-bottom" style="color: green"></i>
    </div>
    <div class="chart-footer" ref="line"></div>
  </div>
</template>

<script>
import echarts from "echarts";
export default {
  name: "lineChart",
  mounted() {
    // 初始化echarts实例
    let myChart = echarts.init(this.$refs.line);
    // 配置数据
    myChart.setOption({
      xAxis: {
        // 隐藏x轴
        show: false,
        type: "category",
      },
      yAxis: {
        // 隐藏y轴
        show: false,
      },
      // 系列
      series: [
        {
          type: "line",
          data: [10, 43, 23, 49, 59, 29, 48, 28, 67, 23, 46, 38, 12],
          // 拐点样式的设置
          itemStyle: {
            opacity: 0,
          },
          // 线条样式
          lineStyle: {
            color: "skyblue",
          },
          // 填充的颜色
          areaStyle: {
            color: {
              type: "linear",
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              colorStops: [
                {
                  offset: 0,
                  color: "skyblue", // 0% 处的颜色
                },
                {
                  offset: 1,
                  color: "white", // 100% 处的颜色
                },
              ],
              global: false, // 缺省为 false
            },
          },
          // 是否平滑曲线显示。
          smooth: 0.5,
        },
      ],
      // 布局调试
      grid: {
        top: 0,
        left: 0,
        bottom: 0,
        right: 0,
      },
    });
  },
};
</script>

<style scoped>
.search {
  margin-right: 20px;
  vertical-align: center;
}
.chart-header {
  color: #bbb;
  font-size: 14px;
}
.chart-content {
  margin: 15px 0;
}
.chart-content span:first-child {
  font-size: 18px;
  margin-right: 20px;
}
.chart-footer {
  width: 100%;
  height: 50px;
}
</style>