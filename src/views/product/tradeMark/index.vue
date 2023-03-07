<template>
  <div>
    <el-button
      type="primary"
      icon="el-icon-plus"
      style="margin-bottom: 10px"
      @click="showDialog"
      >添加</el-button
    >
    <!-- 
        表格组件
        data:表格组件需要显示的数据-------数组类型
        border:添加竖直方向边框
        column属性
        label:显示的标题
        width:对应列的宽度
        align:标题的对齐方式
        prop:对应列内容的字段名
        type:对应列的类型
        注意1：elementUI当中的table组件，展示的数据是一列一列进行展示数据
     -->
    <el-table border style="width: 100%" :data="list">
      <el-table-column type="index" label="序号" width="80px" align="center">
      </el-table-column>
      <el-table-column prop="tmName" label="品牌名称" width="width">
      </el-table-column>
      <el-table-column label="品牌LOGO" width="width">
        <template slot-scope="{ row }">
          <img :src="row.logoUrl" style="width: 100px; height: 100px" />
        </template>
      </el-table-column>
      <el-table-column label="操作" width="width">
        <template slot-scope="{ row }">
          <el-button
            type="warning"
            icon="el-icon-edit"
            size="mini"
            @click="updateTradeMark(row)"
            >修改</el-button
          >
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
            @click="deleteTradeMark(row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页器 -->
    <!-- 当前第几页，数据总条数，每一页展示条数，连续页码数
        current-page:当前页数
        page-size:每页显示条目个数
        total:总条目数
        page-sizes：每页显示个数选择器的选项设置
        layout	组件布局，子组件名用逗号分隔
        size-change	pageSize 改变时会触发	每页条数
        current-change	currentPage 改变时会触发
     -->
    <el-pagination
      style="text-align: center; margin-top: 20px"
      :current-page="page"
      :page-size="limit"
      :total="total"
      :page-sizes="[3, 5, 10]"
      layout="prev, pager, next, jumper,->,sizes,total"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    >
    </el-pagination>
    <!-- dialog对话框 -->
    <el-dialog
      :title="tradeMark.id ? '修改品牌' : '添加品牌'"
      :visible.sync="dialogFormVisible"
    >
      <!-- form表单：model属性，这个属性的作用是，把表单的数据收集到那个对象身上，将来表单验证，也需要这个属性 
      Form 组件提供了表单验证的功能，只需要通过 rules 属性传入约定的验证规则，并将 Form-Item 的 prop 属性设置为需校验的字段名即可
      -->
      <el-form :model="tradeMark" :rules="rules" ref="ruleForm">
        <el-form-item label="品牌名称" prop="tmName">
          <el-input v-model="tradeMark.tmName" autocomplete="off"></el-input>
        </el-form-item>
        <!-- 这里收集数据，不能用v-model，因为不是表单元素
             action:设置图片上传的地址
             :on-success:可以检测到图片上传成功，当图片上传成功，会执行一次
             :before-upload:可以再上传图片之前，会执行一次
         -->
        <el-form-item label="品牌LOGO" prop="logoUrl">
          <el-upload
            class="avatar-uploader"
            action="/dev-api/admin/product/upload"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img
              v-if="tradeMark.logoUrl"
              :src="tradeMark.logoUrl"
              class="avatar"
            />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            <div slot="tip" class="el-upload__tip">
              只能上传jpg/png文件，且不超过500kb
            </div>
          </el-upload>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addOrUpdateTradeMark"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "tradeMark",
  data() {
    // 自定义校验规则
    var validateName = (rule, value, callback) => {
      if (value.length < 2 || value.length > 10) {
        callback(new Error("长度在2到10个字符"));
      } else {
        callback();
      }
    };
    return {
      // 当前页码
      page: 1,
      // 每页记录数
      limit: 3,
      // 数据总条数
      total: 0,
      // 列表展示的数据
      list: [],
      // 是否显示对话框
      dialogFormVisible: false,
      // 收集品牌信息：对象身上的属性不能瞎写，需要看文档
      tradeMark: {
        tmName: "",
        logoUrl: "",
      },
      // 表单验证规则
      rules: {
        // 品牌名称的验证规则
        // required:必须要校验的字段(前面五角星有关系) message 提示信息  trigger:用户行为设置(事件的设置：blur,change)
        tmName: [
          { required: true, message: "请输入品牌名称", trigger: "blur" },
          // 自定义校验规则
          { validator: validateName, trigger: "change" },
        ],
        // 品牌logo的校验
        logoUrl: [{ required: true, message: "请选择品牌图片" }],
      },
    };
  },
  // 组件挂载完毕发请求
  mounted() {
    // 获取列表数据方法
    this.getTradeMarkList();
  },
  methods: {
    // 获取品牌列表的数据
    async getTradeMarkList(pager = 1) {
      this.page = pager;
      // 解构赋值
      let { page, limit } = this;
      // 获取商品列表的接口
      // 当你向服务器发请求的时候，这个函数需要带参数，因此在data中初始化这两个字段
      let result = await this.$API.tradeMark.reqTradeMarkList(page, limit);
      if (result.code == 200) {
        // 分别是展示数据总条数与列表展示的数据
        this.total = result.data.total;
        this.list = result.data.records;
      }
    },
    // 当分页器当前页改变时触发
    handleCurrentChange(pager) {
      // 整理参数
      this.getTradeMarkList(pager);
    },
    // 当分页器某一页需要展示数据条数发生变化时触发
    handleSizeChange(size) {
      // 整理参数
      this.limit = size;
      this.getTradeMarkList();
    },
    // 点击添加品牌时
    showDialog() {
      // 显示对话框
      this.dialogFormVisible = true;
      // 清除数据
      this.tradeMark = { tmName: "", logoUrl: "" };
    },
    // 修改某一个品牌
    updateTradeMark(row) {
      // row:当前选中的品牌
      // 显示对话框
      this.dialogFormVisible = true;
      // 将已有的品牌信息赋值给tradeMark进行展示;
      // 将服务器返回品牌的信息，直接赋值给了tradeMark进行展示
      // 也就是tradeMark存储即为服务器返回品牌信息
      this.tradeMark = { ...row };
    },
    // 文件上传成功时
    handleAvatarSuccess(res, file) {
      this.tradeMark.logoUrl = res.data;
    },
    // 上传文件之前
    beforeAvatarUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 2MB!");
      }
      return isJPG && isLt2M;
    },
    // 添加按钮 添加或修改品牌
    addOrUpdateTradeMark() {
      // 当全部校验字段通过，再去书写业务逻辑
      this.$refs.ruleForm.validate(async (valid) => {
        // 如果全部字段符合条件
        if (valid) {
          this.dialogFormVisible = false;
          // 发请求(添加品牌|修改品牌)
          let result = await this.$API.tradeMark.reqAddOrUpdateTradeMark(
            this.tradeMark
          );
          if (result.code == 200) {
            // 弹出消息：添加品牌成功、修改品牌成功
            this.$message({
              type: "success",
              message: this.tradeMark.id ? "修改品牌成功" : "添加品牌成功",
            });
            // 修改品牌在当前页刷新，添加品牌回到第一页
            this.getTradeMarkList(this.tradeMark.id ? this.page : 1);
          }
        } else {
          console.log("error submit!!");
          return false;
        }
      });
      // 发请求
    },
    // 删除品牌的操作
    deleteTradeMark(row) {
      // 弹框
      this.$confirm(`确定删除${row.tmName}?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          // 当用户点击确认按钮的时候会向服务器发请求
          let result = await this.$API.tradeMark.reqDeleteTradeMark(row.id);
          // 如果删除成功
          if (result.code == 200) {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
            // 再次获取品牌列表
            this.getTradeMarkList(
              this.list.length > 1 ? this.page : this.page - 1
            );
          }
        })
        .catch(() => {
          // 当用户点击取消按钮的时候会触发
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>