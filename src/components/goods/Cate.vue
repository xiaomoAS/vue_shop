<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row>
        <el-col>
          <el-button @click="showAddCateDialog" type="primary">添加商品</el-button>

          <!-- 表格 -->
          <tree-table class="treeTable" :data="cateList" :columns="columns" :selection-type="false" :expand-type="false" border show-index>
            <template slot="isok" slot-scope="info">
              <i style="color: green;" class="el-icon-success" v-if="info.row.cat_deleted === false"></i>
              <i style="color: red;" class="el-icon-error" v-else></i>
            </template>

            <template slot="order" slot-scope="info">
              <el-tag size="mini" v-if="info.row.cat_level === 0">一级</el-tag>
              <el-tag size="mini" v-else-if="info.row.cat_level === 1" type="success">二级</el-tag>
              <el-tag size="mini" v-else type="warning">三级</el-tag>
            </template>

            <template slot="opt">
              <el-button type="primary" size="mini" icon="el-icon-edit">编辑</el-button>
              <el-button type="danger" size="mini" icon="el-icon-delete">删除</el-button>
            </template>
          </tree-table>
          <!-- 分页 -->
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="queryInfo.pagenum"
              :page-sizes="[1, 5, 10, 20]"
              :page-size="queryInfo.pagesize"
              layout="total, sizes, prev, pager, next, jumper"
              :total="total">
            </el-pagination>
        </el-col>
      </el-row>
    </el-card>

    <el-dialog @close="addCateDialogClosed"
      title="添加分类"
      :visible.sync="addCateDialogVisible"
      width="50%">
      <el-form :model="addCateForm" :rules="addCateFormRules" ref="addCateFormRef" label-width="100px">
        <el-form-item label="分类名称: " prop="cat_name">
          <el-input v-model="addCateForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类: ">
            <el-cascader clearable
                v-model="selectedParentCate"
                :options="parentCateList"
                :props="cascaderProps"
                @change="parentCateChange"></el-cascader>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button @click="addCateDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addCate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Cate',
  data () {
    return {
      cateList: [],
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 4
      },
      total: 0,
      columns: [
        {
          label: '分类名称',
          prop: 'cat_name'
        },
        {
          label: '是否有效',
          type: 'template',
          template: 'isok'
        },
        {
          label: '排序',
          type: 'template',
          template: 'order'
        },
        {
          label: '操作',
          type: 'template',
          template: 'opt'
        }
      ],
      addCateDialogVisible: false,
      addCateForm: {
        cat_name: '',
        cat_pid: 0,
        cat_level: 0
      },
      addCateFormRules: {
        cat_name: [
          { required: true, message: '请输入名称', trigger: 'blur' }
        ]
      },
      parentCateList: [],
      selectedParentCate: [],
      cascaderProps: {
        expandTrigger: 'hover',
        children: 'children',
        value: 'cat_id',
        label: 'cat_name',
        checkStrictly: true
      }
    }
  },
  created () {
    this.getCateList()
  },
  methods: {
    async getCateList () {
      const { data: res } = await this.$http.get('categories', { params: this.queryInfo })
      if (res.meta.status !== 200) return this.$message.error('获取商品分类失败')
      this.cateList = res.data.result
      this.total = res.data.total
    },
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getCateList()
    },
    showAddCateDialog () {
      this.getParentCateList()
      this.addCateDialogVisible = true
    },
    async getParentCateList () {
      const { data: res } = await this.$http.get('categories', { params: { type: 2 } })
      if (res.meta.status !== 200) return this.$message.error('获取父级商品分类失败')
      this.parentCateList = res.data
    },
    parentCateChange () {
      if (this.selectedParentCate.length > 0) {
        this.addCateForm.cat_pid = this.selectedParentCate[this.selectedParentCate.length - 1]
        this.addCateForm.cat_level = this.selectedParentCate.length
      } else {
        this.addCateForm.cat_pid = 0
        this.addCateForm.cat_level = 0
      }
    },
    addCate () {
      this.$refs.addCateFormRef.validate(async valid => {
        if (valid) {
          const { data: res } = await this.$http.post('categories', this.addCateForm)
          console.log(res)
          if (res.meta.status !== 201) {
            return this.$message.error('添加分类失败')
          }
          this.$message.success('添加分类成功')
          this.addCateDialogVisible = false
          this.getCateList()
        }
      })
    },
    addCateDialogClosed () {
      this.$refs.addCateFormRef.resetFields()
      this.selectedParentCate = []
      this.addCateForm.cat_pid = 0
      this.addCateForm.cat_level = 0
    }
  }
}
</script>

<style scoped>

</style>
