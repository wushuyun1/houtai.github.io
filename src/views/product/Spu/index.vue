<template>
  <div>
    <el-card style="margin-bottom: 20px">
      <CategorySelect
        @getCategoryId="getCategoryId"
        :show="!scene == 0"
      ></CategorySelect>
    </el-card>
    <el-card>
      <div v-show="scene == 0">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addSpu"
          >添加SPU</el-button
        >
        <!-- 表格 -->
        <el-table style="width: 100%" border :data="records">
          <el-table-column type="index" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="spuName" label="spu名称" width="width">
          </el-table-column
          ><el-table-column prop="description" label="spu描述" width="width">
          </el-table-column
          ><el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row }">
              <el-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加Sku"
                @click="addSku(row)"
              ></el-button>
              <el-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改Spu"
                @click="updateSpu(row)"
              ></el-button>
              <el-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看Spu全部的Sku列表"
                @click="handler(row)"
              ></el-button>
              <el-popconfirm
                title="这是一段内容确定删除吗？"
                @onConfirm="deleteSpu(row)"
              >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  title="删除Spu"
                  slot="reference"
                  style="margin-left: 10px"
                ></el-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页器 -->
        <el-pagination
          style="text-align: center"
          :current-page="page"
          :page-sizes="[1, 2, 3]"
          :page-size="limit"
          layout=" prev, pager, next, jumper, ->,sizes,total"
          :total="total"
          @current-change="handleCurrentChange"
          @size-change="handleSizeChange"
        >
        </el-pagination>
        <!-- dialog对话框 -->
        <el-dialog
          :title="`${spu.spuName}的sku列表`"
          :visible.sync="dialogTableVisible"
          :before-close="close"
        >
          <el-table :data="skuList" v-loading="loading">
            <el-table-column label="名称" prop="skuName"></el-table-column>
            <el-table-column label="价格" prop="price"></el-table-column>
            <el-table-column label="重量" prop="weight"></el-table-column>
            <el-table-column prop="prop" label="默认图片">
              <template slot-scope="{ row }">
                <img
                  :src="row.skuDefaultImg"
                  alt=""
                  style="width: 100px; height: 100px"
                />
              </template>
            </el-table-column>
          </el-table>
        </el-dialog>
      </div>
      <SpuForm v-show="scene == 1" ref="spu" @changScene="changScene"></SpuForm>
      <SkuForm
        v-show="scene == 2"
        ref="sku"
        @changeScenes="changeScenes"
      ></SkuForm>
    </el-card>
  </div>
</template>

<script>
import SpuForm from "./SpuForm";
import SkuForm from "./SkuForm";
export default {
  name: "Spu",
  components: { SpuForm, SkuForm },
  data() {
    return {
      // 各级分类的id
      category1Id: "",
      category2Id: "",
      category3Id: "",
      // 分页器当前第几页
      page: 1,
      // 分页器每页显示数据的条数
      limit: 3,
      // 数据总条数
      total: 0,
      // Spu列表用于展示的数据
      records: [],
      scene: 0, // 0代表展示Spu列表 1代表添加Spu|修改Spu 2代表添加Sku
      // Sku列表的数据
      skuList: [],
      // 是否显示对话框
      dialogTableVisible: false,
      // 查看sku列表对应的spu
      spu: {},
      // 控制loading加载
      loading: true,
    };
  },

  methods: {
    // 自定义函数获取各级分类id
    getCategoryId({ categoryId, level }) {
      if (level == 1) {
        // 清除二三级id
        this.category2Id = "";
        this.category3Id = "";
        // 获得一级分类id
        this.category1Id = categoryId;
      } else if (level == 2) {
        this.category3Id = "";
        this.category2Id = categoryId;
      } else {
        this.category3Id = categoryId;
        // 发请求获取Spu列表
        this.getSpuList();
      }
    },
    // 获取Spu列表数据的方法
    async getSpuList() {
      // 解构赋值
      let { page, limit, category3Id } = this;
      // 发请求获取Spu列表的数据
      let result = await this.$API.spu.reqSpuList(page, limit, category3Id);
      if (result.code == 200) {
        // 得到total总条数和records数据
        this.total = result.data.total;
        this.records = result.data.records;
      }
    },
    // 修改当前页
    handleCurrentChange(pages) {
      // 修改当前页
      this.page = pages;
      // 获取新列表
      this.getSpuList();
    },
    // 修改每页展示数据条数
    handleSizeChange(size) {
      this.limit = size;
      // 重新获取列表
      this.getSpuList();
    },
    // 添加Spu按钮的回调
    addSpu() {
      // 切换场景为1
      this.scene = 1;
      // 通知子组件SpuForm发请求---两个
      this.$refs.spu.addSpuData(this.category3Id);
    },
    // 修改某一个Spu
    updateSpu(row) {
      this.scene = 1;
      this.$refs.spu.initSpuData(row);
    },
    // 自定义事件回调
    changScene({ scene, flag }) {
      // flag这个形参是为了区分保存按钮是添加还是修改
      // 切换结构
      this.scene = scene;
      // 子组件通知父组件切换场景，需要再次获取spu
      if (flag == "修改") {
        this.getSpuList();
      } else {
        this.page = 1;
        this.getSpuList();
      }
    },
    // 删除SPU
    async deleteSpu(row) {
      // 发请求，删除spu
      let result = await this.$API.spu.reqDeleteSpu(row.id);
      if (result.code == 200) {
        this.$message({ type: "success", message: "删除成功" });
        // 代表SPU个数大于1删除的时候停留在当前页，如果SPU个数小于1，回到上一页
        this.records.length > 1 ? this.page : (this.page = this.page - 1);
        this.getSpuList();
      }
    },
    // 添加SKU
    addSku(row) {
      // 切换场景为2
      this.scene = 2;
      const { category1Id, category2Id } = this;
      // 父组件调用子组件的方法，让子组件发请求-----3个
      this.$refs.sku.getData(row, category1Id, category2Id);
    },
    // SkuForm通知父组件修改场景
    changeScenes(scene) {
      this.scene = scene;
    },
    // 查看Sku的按钮的回调
    async handler(spu) {
      // 保存sku信息
      this.spu = spu;
      // 点击这个按钮的时候对话框可见
      this.dialogTableVisible = true;
      // 获取sku列表数据进行展示
      let result = await this.$API.spu.reqSkuList(spu.id);
      if (result.code == 200) {
        this.skuList = result.data;
        this.loading = false;
      }
    },
    // dialog关闭前的回调
    close(done) {
      // 清除sku列表的数据
      this.skuList = [];
      // loading属性再次变为真
      this.loading = true;
      // 关闭对话框
      done();
    },
  },
};
</script>

<style>
</style>