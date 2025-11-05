<template>
  <div>
    <!-- 搜索框 -->
    <el-form :inline="true" :model="searchForm" class="search-form">
      <el-form-item label="题目关键词">
        <el-input v-model="searchForm.keyword" placeholder="请输入关键词"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSearch">查询</el-button>
        <el-button type="success" @click="handleAdd">新增题目</el-button>
      </el-form-item>
    </el-form>

    <!-- 题目表格 -->
    <el-table 
      :data="filteredData" 
      border 
      style="width: 100%; margin-top: 10px;"
    >
      <el-table-column prop="id" label="序号" width="80"></el-table-column>
      <el-table-column prop="questionText" label="题目内容" width="400"></el-table-column>
      <el-table-column label="选项" width="300">
        <template slot-scope="scope">
          <div v-for="(opt, index) in scope.row.options" :key="index">
            {{ ['A', 'B', 'C', 'D'][index] }}. {{ opt }}
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="answer" label="答案" width="80"></el-table-column>
      <el-table-column label="操作" width="200">
        <template slot-scope="scope">
          <el-button size="mini" type="primary" @click="handleEdit(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页组件 -->
    <el-pagination
      background
      layout="prev, pager, next"
      :page-size="pageSize"
      :total="filteredData.length"
      :current-page="currentPage"
      @current-change="handlePageChange"
      style="margin-top: 15px; text-align: right;"
    ></el-pagination>

    <!-- 编辑/新增对话框 -->
    <el-dialog 
      :title="isEdit ? '编辑题目' : '新增题目'" 
      :visible.sync="dialogVisible"
      width="500px"
    >
      <el-form :model="form" :rules="rules" ref="formRef" label-width="100px">
        <el-form-item label="题目内容" prop="questionText">
          <el-input v-model="form.questionText" rows="2" type="textarea"></el-input>
        </el-form-item>
        <el-form-item label="选项A" prop="optiona">
          <el-input v-model="form.optiona"></el-input>
        </el-form-item>
        <el-form-item label="选项B" prop="optionb">
          <el-input v-model="form.optionb"></el-input>
        </el-form-item>
        <el-form-item label="选项C" prop="optionc">
          <el-input v-model="form.optionc"></el-input>
        </el-form-item>
        <el-form-item label="选项D" prop="optiond">
          <el-input v-model="form.optiond"></el-input>
        </el-form-item>
        <el-form-item label="答案" prop="answer">
          <el-input v-model="form.answer" placeholder="请输入A/B/C/D"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 原始数据（模拟数据库）
      tableData: [
        {
          id: 1,
          questionText: "Vue的生命周期钩子中，页面加载完成后调用的是？",
          options: ["created", "mounted", "updated", "destroyed"],
          answer: "B"
        },
        {
          id: 2,
          questionText: "Element UI中，用于展示表格数据的组件是？",
          options: ["el-list", "el-table", "el-grid", "el-data"],
          answer: "B"
        }
      ],
      // 搜索表单
      searchForm: {
        keyword: ""
      },
      // 分页参数
      currentPage: 1,
      pageSize: 5,
      // 对话框控制
      dialogVisible: false,
      isEdit: false, // true:编辑，false:新增
      // 表单数据
      form: {
        id: null,
        questionText: "",
        optiona: "",
        optionb: "",
        optionc: "",
        optiond: "",
        answer: ""
      },
      // 表单验证规则
      rules: {
        questionText: [{ required: true, message: "请输入题目内容", trigger: "blur" }],
        optiona: [{ required: true, message: "请输入选项A", trigger: "blur" }],
        optionb: [{ required: true, message: "请输入选项B", trigger: "blur" }],
        optionc: [{ required: true, message: "请输入选项C", trigger: "blur" }],
        optiond: [{ required: true, message: "请输入选项D", trigger: "blur" }],
        answer: [
          { required: true, message: "请输入答案", trigger: "blur" },
          { pattern: /^[ABCD]$/, message: "答案必须是A/B/C/D", trigger: "blur" }
        ]
      }
    };
  },
  computed: {
    // 过滤后的列表（搜索功能）
    filteredData() {
      if (!this.searchForm.keyword) {
        return this.tableData;
      }
      const keyword = this.searchForm.keyword.toLowerCase();
      return this.tableData.filter(item => 
        item.questionText.toLowerCase().includes(keyword)
      );
    }
  },
  methods: {
    // 分页切换
    handlePageChange(page) {
      this.currentPage = page;
    },

    // 搜索
    handleSearch() {
      this.currentPage = 1; // 搜索后重置到第一页
    },

    // 新增题目
    handleAdd() {
      this.isEdit = false;
      this.dialogVisible = true;
      // 重置表单
      this.$nextTick(() => {
        this.$refs.formRef.resetFields();
        this.form.id = null;
      });
    },

    // 编辑题目
    handleEdit(row) {
      this.isEdit = true;
      this.dialogVisible = true;
      // 填充表单数据
      this.$nextTick(() => {
        this.form = {
          id: row.id,
          questionText: row.questionText,
          optiona: row.options[0],
          optionb: row.options[1],
          optionc: row.options[2],
          optiond: row.options[3],
          answer: row.answer
        };
      });
    },

    // 提交表单（新增/编辑）
    handleSubmit() {
      this.$refs.formRef.validate(valid => {
        if (valid) {
          // 构造选项数组
          const options = [
            this.form.optiona,
            this.form.optionb,
            this.form.optionc,
            this.form.optiond
          ];

          if (this.isEdit) {
            // 编辑操作：更新现有数据
            const index = this.tableData.findIndex(item => item.id === this.form.id);
            this.tableData[index] = {
              ...this.tableData[index],
              questionText: this.form.questionText,
              options,
              answer: this.form.answer
            };
            this.$message.success("编辑成功！");
          } else {
            // 新增操作：添加新数据
            const newId = Math.max(...this.tableData.map(item => item.id), 0) + 1;
            this.tableData.push({
              id: newId,
              questionText: this.form.questionText,
              options,
              answer: this.form.answer
            });
            this.$message.success("新增成功！");
          }
          // 关闭对话框
          this.dialogVisible = false;
        }
      });
    },

    // 删除题目
    handleDelete(id) {
      this.$confirm("确定要删除该题目吗？", "提示", {
        type: "warning"
      }).then(() => {
        this.tableData = this.tableData.filter(item => item.id !== id);
        this.$message.success("删除成功！");
      }).catch(() => {
        this.$message.info("已取消删除");
      });
    }
  }
};
</script>

<style scoped>
.search-form {
  margin-bottom: 15px;
}
</style>