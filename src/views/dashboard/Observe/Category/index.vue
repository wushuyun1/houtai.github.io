<template>
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <span>销售额度占比</span>
      <div class="radio-group">
        <el-radio-group v-model="tabPosition" size="mini">
          <el-radio-button label="quan">全部渠道</el-radio-button>
          <el-radio-button label="xianshang">线上</el-radio-button>
          <el-radio-button label="mendian">门店</el-radio-button>
        </el-radio-group>
      </div>
    </div>
    <div class="echarts" ref="pie"></div>
  </el-card>
</template>

<script>
import echarts from "echarts";
export default {
  name: "Category",
  data() {
    return {
      tabPosition: "quan",
    };
  },
  mounted() {
    let myChart = echarts.init(this.$refs.pie);
    myChart.setOption({
      title: {
        text: "搜索引擎",
        sbutext: 1028,
      },
      tooltip: {
        trigger: "item",
      },
      series: [
        {
          name: "Access From",
          type: "pie",
          radius: ["40%", "70%"],
          avoidLabelOverlap: false,
          itemStyle: {
            borderRadius: 10,
            borderColor: "#fff",
            borderWidth: 2,
          },
          label: {
            show: false,
            position: "center",
          },
          // emphasis: {
          //   label: {
          //     show: true,
          //     fontSize: "40",
          //     fontWeight: "bold",
          //   },
          // },
          labelLine: {
            show: true,
          },
          data: [
            { value: 1048, name: "Search Engine" },
            { value: 735, name: "Direct" },
            { value: 580, name: "Email" },
            { value: 484, name: "Union Ads" },
            { value: 300, name: "Video Ads" },
          ],
        },
      ],
    });
  },
};
</script>

<style>
.radio-group {
  float: right;
}
.clearfix {
  margin-bottom: 0;
}
.echarts {
  width: 100%;
  height: 250px;
}
</style>