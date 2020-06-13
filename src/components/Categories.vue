<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card v-loading="status">
      <el-row>
        <el-col>
          <el-button type="primary" @click="addClassify">添加分类</el-button>
        </el-col>

        <!-- 表格 -->
        <tree-table
          :data="cateList"
          :columns="columns"
          :selection-type="false"
          show-index
          :index-text="'#'"
          border
          :expand-type="false"
          :show-row-hover="false"
        >
          <!-- 是否有效 -->
          <template slot="iseffective" slot-scope="scope">
            <!-- {{scope.row.cat_name}} -->
            <i
              class="el-icon-success"
              v-if="scope.row.cat_name.cate_deleted"
              style="color:lightgreen;"
            ></i>
            <i class="el-icon-error" v-else style="color:red;"></i>
          </template>
          <template slot="sort" slot-scope="scope">
            <!-- 排序 -->
            <el-tag v-if="scope.row.cat_level == 0">标签一</el-tag>
            <el-tag type="success" v-if="scope.row.cat_level == 1">标签二</el-tag>
            <el-tag type="info" v-if="scope.row.cat_level == 2">标签三</el-tag>
          </template>
          <!-- 操作 -->
          <template slot="operate" slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row)">编辑</el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="del(scope.row.cat_id)"
            >删除</el-button>
          </template>
        </tree-table>
        <!-- 分页区 -->
      </el-row>
      <el-pagination
        background
        layout="prev, pager, next"
        :page-size="querInfo.pagesize"
        :total="sumNum"
        @current-change="handleSizeChange"
      ></el-pagination>
    </el-card>

    <!-- 添加分类对话框 -->
    <el-dialog title="添加分类" :visible.sync="dialogVisible" width="50%">
      <!-- 添加分类表单 -->
      <el-form
        :model="addcate"
        :rules="addcaterules"
        ref="addcateref"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="分类名称" prop="name">
          <el-input v-model="addcate.name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类">
          <!-- options用来表示数据源 -->
          <el-cascader
            v-model="value"
            :options="parentClass"
            :props="{ expandTrigger: 'hover' }"
          ></el-cascader>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="cateCancel">取 消</el-button>
        <el-button type="primary" @click="cateDefine">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "categories",
  data() {
    return {
      // 父级分类id
      cat_pid: 0,
      // 分类名称
      cat_name: "",
      // 分类等级  默认添加的是1级分类
      cat_level: 0,
      // 表单验证规则
      addcaterules: {
        name: [{ required: true, message: "请输入活动名称", trigger: "blur" }]
      },
      // 分类表单内容
      addcate: {
        name: ""
      },
      // 添加分类对话框是否显示
      dialogVisible: false,
      // 数据列表
      cateList: [],

      querInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },

      // 总数据条数
      sumNum: 30,

      //  为table指定列的定义
      columns: [
        {
          label: "分类名称",
          prop: "cat_name"
        },
        {
          label: "是否有效",
          type: "template",
          template: "iseffective"
        },
        {
          label: "排序",
          type: "template",
          template: "sort"
        },
        {
          label: "操作",
          type: "template",
          template: "operate"
        }
      ],
      status: false,
      // 父级分类
      parentClass: [],
      value:[]
    };
  },
  methods: {
    // 获取父级分类的数据列表
    async getPcate() {
      const { data } = await this.$axios.get("categories", {
        params: { type: 2 }
      });
      console.log(data);
      if (data.meta.status == 200) {
        this.parentClass = data.data;
      }
    },

    // 添加分类取消
    cateCancel() {
      this.dialogVisible = false;
      this.$refs.addcateref.resetFields();
    },
    // 确定添加
    cateDefine() {
      let that = this;
      that.$refs.addcateref.validate(res => {
        console.log(res);
        //  that.$axios.

        // this.dialogVisible = false
        // this.$refs.addcateref.resetFields()
      });
    },
    // 添加分类对话框
    addClassify() {
      this.dialogVisible = true;
      this.getPcate();
    },
    // 分页
    handleSizeChange(pagesize) {
      console.log(pagesize);
      this.querInfo.pagenum = pagesize;
      this.getList();
    },
    // 删除
    del(id) {
      console.log(id);
    },
    // 编辑
    edit() {},
    async getList() {
      let that = this;
      that.status = true;
      const { data } = await that.$axios.get("categories", {
        params: this.querInfo
      });
      that.status = false;
      console.log(data);
      if (data.meta.status == 200) {
        // 总条数
        that.sumNum = data.data.total;
        console.log(that.sumNum);
        // 列表
        that.cateList = data.data.result;
      }
    }
  },
  created() {
    console.log(this);
    this.getList();
  }
};
</script>

<style lang="scss" scoped>
.el-card {
  margin-top: 20px;
}
.el-col {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
  text-align: center;
}
</style>