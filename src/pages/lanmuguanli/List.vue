<template>
  <div>
    <h2>栏目管理</h2>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="categories">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="栏目名称"></el-table-column>
      <el-table-column prop="num" label="序号"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="#" @click.prevent="toDeleteHandler(slot.row.id)">
            <i class="el-icon-delete"></i>
          </a>
          <a href="#" @click.prevent="toUpdateHandler(slot.row)">
            <i class="el-icon-edit"></i>
          </a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 模态框 -->
    <el-dialog :title="title" :visible.sync="visible" width="60%">
      <!-- ---{{form}} -->
      <el-form :model="form" :rules="abcddd" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num"></el-input>
        </el-form-item>
        <el-form-item label="父栏目">
          <el-input v-model="form.parentId"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->
  </div>
</template>

<script>
import request from "@/utils/request";
import querystring from "querystring";
export default {
  // 用于存放网页中需要调用的方法
  methods: {
    loadData() {
      let url = "http://[::1]:6677/category/findAll";
      request.get(url).then(response => {
        // 将查询结果设置到customers中，this指向外部函数的this
        this.categories = response.data;
      });
    },
    submitHandler() {
      let url = "http://[::1]:6677/category/saveOrUpdate";
      request({
        url,
        method: "POST",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: querystring.stringify(this.form)
      }).then(response => {
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type: "success",
          message: response.message
        });
      });
    },
    toDeleteHandler(id) {
      this.$confirm("此操作将永久删除该条目, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let url = "http://[::1]:6677/category/deleteById?id=";
        request.get(url + id).then(response => {
          this.$message({
            type: "success",
            message: response.message
          });
        });
      });
    },
    toUpdateHandler(row) {
      this.title = "更新栏目信息";
      this.form = row;
      this.visible = true;
    },
    closeModalHandler() {
      this.visible = false;
    },
    toAddHandler() {
      this.title = "添加栏目信息";
      this.form = {};
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data() {
    return {
      title: "录入栏目信息",
      visible: false,
      categories: [],
      form: {},
      abcddd: {
        id: [{ required: true, message: "id不能为空", trigger: "blur" }],
        name: [{ required: true, message: "name不能为空", trigger: "blur" }],
        num: [{ required: true, message: "价格不能为空", trigger: "blur" }],
      }
    };
  },
  created() {
    // this为当前vue实例对象
    // vue实例创建完毕
    this.loadData();
  }
};
</script>

<style scoped>
</style>
