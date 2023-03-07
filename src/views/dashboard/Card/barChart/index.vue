<template>
  <div ref="line">123</div>
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
          type: "bar",
          data: [10, 43, 23, 49, 59, 29, 48, 28, 67, 23, 46, 38, 12],
          color: "blue",
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
div {
  width: 100%;
  height: 100%;
}
</style>