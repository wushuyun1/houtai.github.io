<template>
  <div>
    <!-- table表格 -->
    <el-table style="width: 100%" border :data="records">
      <el-table-column type="index" label="序号" width="80" align="center">
      </el-table-column>
      <el-table-column prop="skuName" label="名称" width="200">
      </el-table-column>
      <el-table-column prop="skuDesc" label="描述" width="200">
      </el-table-column>
      <el-table-column prop="prop" label="默认图片" width="120">
        <template slot-scope="{ row }">
          <img
            :src="row.skuDefaultImg"
            alt=""
            style="width: 80px; height: 80px"
          />
        </template>
      </el-table-column>
      <el-table-column prop="weight" label="重量(KG)" width="100">
      </el-table-column>
      <el-table-column prop="price" label="价格(元)" width="80">
      </el-table-column>
      <el-table-column prop="prop" label="操作" width="width">
        <template slot-scope="{ row }">
          <el-button
            type="success"
            icon="el-icon-bottom"
            size="mini"
            v-if="row.isSale == 1"
            @click="cancel(row)"
          ></el-button>
          <el-button
            type="info"
            icon="el-icon-top"
            size="mini"
            v-else
            @click="sale(row)"
          ></el-button>
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
            @click="edit"
          ></el-button>
          <el-button
            type="info"
            icon="el-icon-info"
            size="mini"
            @click="getSkuInfo(row)"
          ></el-button>
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
          ></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页器 -->
    <el-pagination
      style="text-align: center"
      :current-page="page"
      :page-sizes="[3, 5, 10]"
      :page-size="limit"
      layout="prev, pager, next, jumper,->,sizes, total"
      :total="total"
      @current-change="getSkuList"
      @size-change="handleSizeChange"
    >
    </el-pagination>
    <!-- 抽屉组件 :before-close="handleClose" -->
    <el-drawer
      :visible.sync="drawer"
      direction="rtl"
      size="50%"
      :show-close="false"
    >
      <el-row>
        <el-col :span="5">名称</el-col>
        <el-col :span="16">{{ skuInfo.skuName }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="5">描述</el-col>
        <el-col :span="16">{{ skuInfo.skuDesc }}</el-col> </el-row
      ><el-row>
        <el-col :span="5">价格</el-col>
        <el-col :span="16">{{ skuInfo.price }}元</el-col> </el-row
      ><el-row>
        <el-col :span="5">平台属性</el-col>
        <el-col :span="16">
          <el-tag
            style="margin-left: 10px"
            type="success"
            v-for="attr in skuInfo.skuAttrValueList"
            :key="attr.id"
            >{{ `${attr.attrId}:${attr.valueId}` }}</el-tag
          >
        </el-col> </el-row
      ><el-row>
        <el-col :span="5">商品图片</el-col>
        <el-col :span="16">
          <!-- 轮播图 -->
          <el-carousel height="150px">
            <el-carousel-item
              v-for="item in skuInfo.skuImageList"
              :key="item.id"
            >
              <img
                :src="item.imgUrl"
                alt=""
                style="width: 300px; height: 300px"
              />
            </el-carousel-item>
          </el-carousel>
        </el-col>
      </el-row>
    </el-drawer>
  </div>
</template>

<script>
export default {
  name: "Sku",
  data() {
    return {
      // 当前第几页
      page: 1,
      // 每一页显示的数据条数
      limit: 10,
      // 数据总条数
      total: 0,
      // sku列表的数据
      records: [],
      // 控制抽屉是否显示
      drawer: false,
      // sku详情的数据
      skuInfo: {},
    };
  },
  // 组件挂载完毕
  mounted() {
    // 获取sku列表的方法
    this.getSkuList();
  },
  methods: {
    // 发请求获取sku列表数据
    async getSkuList(pages = 1) {
      this.page = pages;
      let { page, limit } = this;
      let result = await this.$API.sku.reqSkuList(page, limit);
      if (result.code == 200) {
        this.total = result.data.total;
        this.records = result.data.records;
      }
    },
    // 改变每页展示的条数
    handleSizeChange(limit) {
      // 改变参数
      this.limit = limit;
      this.getSkuList();
    },
    // 下架
    async cancel(spu) {
      let result = await this.$API.sku.reqCancel(spu.id);
      if (result.code == 200) {
        // this.getSkuList();
        // 不用重新发请求也可以，页面展示不受影响，重新加载依旧是下架状态
        spu.isSale = 0;
        this.$message({ type: "success", message: "下架成功" });
      }
    },
    // 上架
    async sale(spu) {
      let result = await this.$API.sku.reqSale(spu.id);
      if (result.code == 200) {
        spu.isSale = 1;
        this.$message({ type: "success", message: "上架成功" });
      }
    },
    edit() {
      this.$message({ type: "info", message: "该模块有待开发" });
    },
    // 获取Sku详情的方法
    async getSkuInfo(spu) {
      this.drawer = true;
      let result = await this.$API.sku.reqSkuById(spu.id);
      if (result.code == 200) {
        this.skuInfo = result.data;
      }
    },
  },
};
</script>

<style>
.el-carousel__item h3 {
  color: #475669;
  font-size: 14px;
  opacity: 0.75;
  line-height: 150px;
  margin: 0;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
}
.el-carousel__button {
  width: 10px;
  height: 10px;
  background-color: pink;
  border-radius: 50%;
}
</style>
<style scoped>
.el-row .el-col-5 {
  font-size: 18px;
  text-align: right;
}
.el-col {
  margin: 10px;
}
</style>