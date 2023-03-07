<template>
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <span>线上热门搜索</span>
      <el-dropdown class="right">
        <span class="el-dropdown-link">
          <i class="el-icon-more"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>黄金糕</el-dropdown-item>
          <el-dropdown-item>狮子头</el-dropdown-item>
          <el-dropdown-item>螺蛳粉</el-dropdown-item>
          <el-dropdown-item>双皮奶</el-dropdown-item>
          <el-dropdown-item>蚵仔煎</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
    <div>
      <el-row :gutter="10">
        <el-col :span="12"><lineChart></lineChart></el-col>
        <el-col :span="12"><lineChart></lineChart></el-col>
      </el-row>
    </div>
    <!-- table表格 -->
    <div class="table">
      <el-table :data="tableData" style="width: 100%" border>
        <el-table-column label="排名" width="80"> </el-table-column>
        <el-table-column label="搜索关键字"> </el-table-column>
        <el-table-column label="用户数" sortable> </el-table-column>
        <el-table-column label="周涨幅" sortable> </el-table-column>
      </el-table>
    </div>
    <!-- 分页器 -->
    <div class="pagination">
      <el-pagination layout="prev, pager, next" :total="1000"> </el-pagination>
    </div>
  </el-card>
</template>

<script>
import lineChart from "./lineChart";
export default {
  name: "Search",
  data() {
    return {
      tableData: [{}],
    };
  },
  components: { lineChart },
};
</script>

<style>
.right {
  float: right;
}
.pagination {
  float: right;
}
</style>