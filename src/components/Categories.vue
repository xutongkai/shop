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
          <el-button type="primary">添加分类</el-button>
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
      <el-pagination background layout="prev, pager, next" :total="sumNum"></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "categories",
  data() {
    return {
      // 数据列表
      cateList: [],

      querInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },

      // 总数据条数
      sumNum: 3,

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
      status:false
    };
  },
  methods: {
    // 删除
    del(id) {
      console.log(id);
    },
    // 编辑
    edit() {},
    async getList() {
      let that = this;
      that.status = true
      const { data } = await that.$axios.get("categories", {
        params: this.querInfo
      });
      that.status = false
      console.log(data);
      if (data.meta.status == 200) {
        // 总条数
        that.sumNum = data.data.total;
        console.log(that.sumNum)
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
</style>