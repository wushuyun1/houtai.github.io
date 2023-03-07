<template>
  <div>
    <el-row :gutter="10">
      <el-col :span="6">
        <el-card>
          <Detail title="总销售额" count="￥126560">
            <template slot="echarts">
              <span> 周同比&nbsp;&nbsp;&nbsp;56.67%</span>
              <svg
                t="1664101132447"
                class="icon"
                viewBox="0 0 1024 1024"
                version="1.1"
                xmlns="http://www.w3.org/2000/svg"
                p-id="2268"
                width="10"
                height="10"
              >
                <path
                  d="M960 704L512 256l-448 448z"
                  fill="#d81e06"
                  p-id="2269"
                ></path>
              </svg>
              &nbsp;&nbsp;<span>日同比&nbsp;&nbsp;&nbsp;19.16%</span>&nbsp;
              <svg
                t="1664101308958"
                class="icon"
                viewBox="0 0 1024 1024"
                version="1.1"
                xmlns="http://www.w3.org/2000/svg"
                p-id="3950"
                width="10"
                height="10"
              >
                <path
                  d="M64 320l448 448 448-448z"
                  fill="#1afa29"
                  p-id="3951"
                ></path>
              </svg>
            </template>
            <template slot="footer">
              <span>日销售额￥&nbsp;&nbsp;12423</span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card>
          <Detail title="访问量" count="88460">
            <template slot="echarts"> <lineChart></lineChart> </template>
            <template slot="footer">
              <span>日访问量&nbsp;&nbsp;1234</span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card>
          <Detail title="支付笔数" count="88460">
            <template slot="echarts">
              <barChart></barChart>
            </template>
            <template slot="footer">
              <span>转化率&nbsp;&nbsp;65%</span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card>
          <Detail title="运营活动效果" count="78%">
            <template slot="echarts">
              <nprogress></nprogress>
            </template>
            <template slot="footer">
              <span
                >周同比&nbsp;&nbsp;12%&nbsp;&nbsp;
                <i class="el-icon-caret-top" style="color: red"></i>
                日同比&nbsp;&nbsp;11%</span
              >
              <i class="el-icon-caret-bottom" style="color: green"></i>
            </template>
          </Detail>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import Detail from "./Detail";
import lineChart from "./lineChart";
import barChart from "./barChart";
import nprogress from "./nprogress";
export default {
  name: "Card",
  components: { Detail, lineChart, barChart, nprogress },
};
</script>

<style>
</style>