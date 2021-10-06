<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row>
        <el-col>
          <el-button type="primary">添加角色</el-button>
        </el-col>
      </el-row>
    </el-card>

    <el-table :data="rolesList">
      <el-table-column type="expand">
        <template slot-scope="roles">
          <el-row :class="['bdbottom', 'vcenter', i1 === 0 ? 'bdtop' : '']" v-for="(item1, i1) in roles.row.children" :key="item1.id">
            <el-col :span="5">
              <el-tag closable @close="removeById(roles.row ,item1.id)">{{item1.authName}}</el-tag>
              <i class="el-icon-caret-right"></i>
            </el-col>
            <el-col :span="19">
             <el-row :class="[i2 === 0 ? '' : 'bdtop', 'vcenter']" v-for="(item2, i2) in item1.children" :key="item2.id">
              <el-col :span="6">
                <el-tag closable @close="removeById(roles.row ,item2.id)" type="success">{{item2.authName}}</el-tag>
                <i class="el-icon-caret-right"></i>
              </el-col>
              <el-col :span="18">
                <el-tag closable @close="removeById(roles.row ,item3.id)" v-for="(item3) in item2.children" :key="item3.id" type="warning">{{item3.authName}}</el-tag>
              </el-col>
             </el-row>
            </el-col>
          </el-row>
        </template>
      </el-table-column>
      <el-table-column type="index" label="#"></el-table-column>
      <el-table-column label="角色名称" prop="roleName"></el-table-column>
      <el-table-column label="角色描述" prop="roleDesc"></el-table-column>
      <el-table-column label="操作" width="300px">
          <template slot-scope="scope">
            <el-button size="mini" type="primary" icon="el-icon-edit">编辑</el-button>
            <el-button size="mini" type="danger" icon="el-icon-delete">删除</el-button>
            <el-button @click="showSetRightDialog(scope.row)" size="mini" type="warning" icon="el-icon-setting">分配权限</el-button>
          </template>
      </el-table-column>
    </el-table>

    <el-dialog @close="setRightDialogClose"
      title="分配角色权限"
      :visible.sync="setRightDialogVisible"
      width="50%">
      <el-tree ref="treeRef" show-checkbox default-expand-all :default-checked-keys="defKeys" node-key="id" :data="rightsList" :props="treeProps" ></el-tree>
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRightDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="allocateRights">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Roles',
  data () {
    return {
      rolesList: [],
      setRightDialogVisible: false,
      rightsList: [],
      treeProps: {
        label: 'authName',
        children: 'children'
      },
      defKeys: [],
      roleId: ''
    }
  },
  created () {
    this.getRolesList()
  },
  methods: {
    async getRolesList () {
      const { data: res } = await this.$http.get('roles')
      if (res.meta.status !== 200) return this.$message.error('获取角色列表失败')
      this.rolesList = res.data
    },
    removeById (role, rightId) {
      this.$confirm('此操作将永久删除该权限, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        const { data: res } = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
        if (res.meta.status !== 200) return this.$message.error('删除权限失败')
        role.children = res.data
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    async showSetRightDialog (role) {
      this.roleId = role.id
      const { data: res } = await this.$http.get('rights/tree')
      if (res.meta.status !== 200) return this.$http.error('获取权限失败')
      this.rightsList = res.data
      this.getDefKeys(role, this.defKeys)
      this.setRightDialogVisible = true
    },
    getDefKeys (node, arr) {
      if (!node.children) {
        return arr.push(node.id)
      } else {
        node.children.forEach(element => {
          this.getDefKeys(element, arr)
        })
      }
    },
    setRightDialogClose () {
      this.defKeys = []
    },
    async allocateRights () {
      const keys = [
        ...this.$refs.treeRef.getCheckedKeys(),
        ...this.$refs.treeRef.getHalfCheckedKeys()
      ]

      const idstr = keys.join(',')
      const { data: res } = await this.$http.post(`roles/${this.roleId}/rights`, { rids: idstr })
      if (res.meta.status !== 200) return this.$message.error('分配权限失败')
      this.setRightDialogVisible = false
      this.$message.success('分配成功')
      this.getRolesList()
    }
  }
}
</script>

<style scoped>

</style>
