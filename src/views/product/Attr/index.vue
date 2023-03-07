<template>
  <div>
    <el-card style="margin-bottom: 20px">
      <CategorySelect
        @getCategoryId="getCategoryId"
        :show="!isShowTable"
      ></CategorySelect>
    </el-card>
    <el-card>
      <div v-show="isShowTable">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addAttr"
          >添加属性</el-button
        >
        <!-- 表格 展示平台属性 -->
        <el-table :data="attrList" border style="width: 100%">
          <el-table-column type="index" label="序号" align="center" width="80">
          </el-table-column>
          <el-table-column prop="attrName" label="属性名称" width="150">
          </el-table-column>
          <el-table-column prop="prop" label="属性值列表">
            <template slot-scope="{ row }">
              <el-tag
                type="success"
                style="margin-left: 20px"
                v-for="attrValue in row.attrValueList"
                :key="attrValue.id"
                >{{ attrValue.valueName }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="150">
            <template slot-scope="{ row }">
              <el-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                @click="updateAttr(row)"
              ></el-button>
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
              ></el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <!-- 添加属性|修改属性的结构 -->
      <div v-show="!isShowTable">
        <el-form label-width="80px" :inline="true" :model="attrInfo">
          <el-form-item label="属性名" size="mini">
            <el-input
              placeholder="请输入属性名"
              v-model="attrInfo.attrName"
            ></el-input>
          </el-form-item>
        </el-form>
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!attrInfo.attrName"
          @click="addAttrValue"
          >添加属性值</el-button
        >
        <el-button @click="isShowTable = true">取消</el-button>
        <el-table
          :data="attrInfo.attrValueList"
          style="width: 100%; margin: 20px 0"
          border
        >
          <el-table-column type="index" label="序号" align="center" width="80">
          </el-table-column>
          <el-table-column prop="prop" label="属性值名称">
            <template slot-scope="{ row, $index }">
              <el-input
                v-if="row.flag"
                v-model="row.valueName"
                placeholder="请输入属性值"
                size="mini"
                @blur="toLook(row)"
                @keyup.native.enter="toLook(row)"
                :ref="$index"
              ></el-input>
              <span
                v-else
                @click="toEdit(row, $index)"
                style="display: block"
                >{{ row.valueName }}</span
              >
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作">
            <template slot-scope="{ row }">
              <!-- 气泡确认框 -->
              <el-popconfirm
                :title="`确认删除${row.valueName}吗？`"
                @onConfirm="deleteAttrValue(row)"
              >
                <el-button
                  slot="reference"
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                ></el-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-button
          type="primary"
          @click="addOrUpdateAttr"
          :disabled="attrInfo.attrValueList.length < 1"
          >保存</el-button
        >
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
// 按需引入lodash当中的深拷贝
import cloneDeep from "lodash/cloneDeep";
export default {
  name: "Attr",
  data() {
    return {
      category1Id: "",
      category2Id: "",
      category3Id: "",
      // 接受平台属性的数据
      attrList: [],
      // 这个属性控制table表格显示与隐藏的
      isShowTable: true,
      // 收集新增属性|修改属性使用的
      attrInfo: {
        attrName: "",
        attrValueList: [],
        categoryId: "",
        categoryLevel: "3",
      },
    };
  },
  methods: {
    // 自定义事件回调
    async getCategoryId({ categoryId, level }) {
      // 区分三级分类相应的id,以及父组件进行存储
      if (level == 1) {
        this.category1Id = categoryId;
        this.category2Id = "";
        this.category3Id = "";
      } else if (level == 2) {
        this.category2Id = categoryId;
        this.category3Id = "";
      } else if (level == 3) {
        // 代表三级分类的id有了
        this.category3Id = categoryId;
        // 发请求，获取品牌属性
        this.getAttrList();
      }
    },
    // 获取平台属性的数据
    // 当用户确定三级分类的数据的时候，可以向服务器发请求获取平台属性进行展示
    async getAttrList() {
      let { category1Id, category2Id, category3Id } = this;
      let result = await this.$API.attr.reqAttrList(
        category1Id,
        category2Id,
        category3Id
      );
      if (result.code == 200) {
        this.attrList = result.data;
      }
    },
    // 添加属性值的回调
    addAttrValue() {
      // 向属性值的数组里面添加元素
      // attrId:是你相应的属性的id,目前而言我们是添加属性的操作，还没有相应的属性的id,目前我们带给服务器的id为underfined
      // valueName:相应属性值的名称
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id, // 对于修改某一个属性时，可以在已有的属性基础之上新增新的属性值
        valueName: "",
        flag: true,
      });
      // flag属性：给每一个属性值添加一个标记flag，用户切换查看模式和编辑模式，好处，每个属性值可以
      // 控制自己的模式切换
      this.$nextTick(() => {
        this.$refs[this.attrInfo.attrValueList.length - 1].focus();
      });
    },
    // 添加属性按钮的回调
    addAttr() {
      // 切换table显示与隐藏
      this.isShowTable = false;
      // 清除数据
      // 手机三级分类的id
      this.attrInfo = {
        attrName: "",
        attrValueList: [],
        categoryId: this.category3Id,
        categoryLevel: "3",
      };
    },
    // 修改属性
    updateAttr(row) {
      // 切换table显示与隐藏
      this.isShowTable = false;
      // 将选中的属性赋值给attrInfo
      // 由于数据结构当中存在对象里面套数组，数组里面又套对象，因此需要使用深拷贝解决这类问题
      // 深拷贝，浅拷贝在面试的时候出现频率很高，切记达到手写深拷贝浅拷贝
      this.attrInfo = cloneDeep(row);
      // 在修改某一个属性的时候，将相应的属性值元素添加上flag这个标记
      this.attrInfo.attrValueList.forEach((item) => {
        // 因为Vue无法检测到普通的新增属性，这样书写的属性并非响应式
        // 第一个参数:对象 第二个参数：添加新的响应式属性 第三个参数：新的属性的属性值
        this.$set(item, "flag", false);
      });
    },
    // 失去焦点的事件---切换到查看模式，展示span
    toLook(row) {
      // 如果属性值为空，不能作为新的属性值，需要给用户提示，让他输入一个其他的属性值
      if (row.valueName.trim() == "") {
        this.$message("请你输入一个正常的属性值");
        return;
      }
      // 新增的属性值不能与已有的属性值重复
      let result = this.attrInfo.attrValueList.some((item) => {
        // 需要将row从判断的时候去除
        // row最新增的属性值【数组的最后一项元素】
        // 判断的时候需要把已有的数组当中新增的这个属性去除
        if (row !== item) {
          return row.valueName == item.valueName;
        }
      });
      if (result) return;
      // row：形参，是当前用户最新添加的属性值
      // 当前编辑模式变为查看模式【让input消失，显示span】
      row.flag = false;
    },
    // 点击span的回调，变为编辑模式
    toEdit(row, index) {
      row.flag = true;
      // 获取input节点，实现自动聚焦
      // 需要注意：点击span的时候，切换为input变为编辑模式，但是需要注意，对于浏览器而言页面重绘是耗时间的
      // 点击span的时候，重绘一个input他是需要耗费时间，因此我们不可能一点击span立马获取到input
      // $nextTick,当节点渲染完毕了，会执行一次
      this.$nextTick(() => {
        // 获取相应的input表单元素实现聚焦
        this.$refs[index].focus();
      });
    },
    // 气泡确认框确认按钮的回调
    // 最新版本的ElementUI---2.15.7,目前模板中的版本号2.13.x
    deleteAttrValue(row) {
      // 当前删除属性值的操作是不需要发请求的
      this.attrInfo.attrValueList.splice(row, 1);
    },
    // 保存按钮，进行添加属性或者修改属性的操作
    async addOrUpdateAttr() {
      // 整理参数：1，如果用户添加很多属性值，且属性值为空的不应该提交服务器
      // 提交服务器数据当中不应该有flag字段
      this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter(
        (item) => {
          // 过滤掉属性值不是空的
          if (item.valueName !== "") {
            // 删除掉flag属性
            delete item.flag;
            return true;
          }
        }
      );
      try {
        // 发请求
        await this.$API.attr.reqAddOrUpdateAttr(this.attrInfo);
        // 展示平台属性的信号量进行切换
        this.isShowTable = true;
        // 提示消失
        this.$message({ type: "success", message: "保存成功" });
        // 保存成功后需要再次获取平台属性进行展示
        this.getAttrList();
      } catch (error) {
        this.$message("保存失败");
      }
    },
  },
};
</script>

<style>
</style>