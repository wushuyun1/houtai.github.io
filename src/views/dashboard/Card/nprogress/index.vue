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
        // 最大值与最小值的设置
        min: 0,
        max: 100,
      },
      yAxis: {
        // 隐藏y轴
        show: false,
        // 均分
        type: "category",
      },
      // 系列
      series: [
        {
          type: "bar",
          data: [78],
          color: "yellowgreen",
          // 柱状图的宽度
          barWidth: 10,
          // 背景颜色设置
          showBackground: true,
          backgroundStyle: {
            color: "#eee",
          },
          // 文本
          label: {
            show: true,
            formatter: "|",
            position: "right",
          },
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