<template>
  <div class="users">
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <hr />
    <!-- 搜索 -->
    <el-input placeholder="请输入搜索关键字" v-model="query" class="input-with-select">
      <el-button slot="append" icon="el-icon-search" @click="seach"></el-button>
    </el-input>
    <el-button class="addBtn" plain type="success" @click="adduser">添加用户</el-button>
    <!-- 表格 -->
    <el-table border :data="userlist" style="width: 100%">
      <el-table-column prop="username" label="姓名" width="180"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>

      <el-table-column prop="mobile" label="电话"></el-table-column>
      <el-table-column label="用户状态">
        <template v-slot:default="obj">
          <el-switch
            v-model="obj.row.mg_state"
            active-color="#13ce66"
            inactive-color="#ff4949"
            @change="change(obj.row)"
          ></el-switch>
        </template>
      </el-table-column>
      <!-- 三个按钮 -->

      <el-table-column label="操作">
        <template v-slot:default="obj">
          <el-button type="primary" icon="el-icon-edit" plain @click="edit(obj.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete" plain @click="del(obj.row.id)"></el-button>
          <el-button type="success" icon="el-icon-check" plain>分配角色</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div class="block">
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pagenum"
        :page-sizes="[2, 4, 6, 8]"
        :page-size="pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </div>
    <!-- 添加对话框 -->
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%" @close="closeDialog">
      <el-form ref="form" :rules="rules" :model="form" label-width="80px">
        <el-form-item label="用户名" prop="username" status-icon>
          <el-input placeholder="请输入用户名" v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password" status-icon>
          <el-input type="password" placeholder="请输入密码" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input placeholder="请输入邮箱" v-model="form.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input placeholder="请输入手机号" v-model="form.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="add">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 修改对话框 -->
    <el-dialog title="修改信息" :visible.sync="editVisible" width="30%">
      <el-form ref="editForm" :rules="rules" :model="editForm" label-width="80px">
        <el-form-item label="用户名">
          <el-tag type="info">{{ editForm.username }}</el-tag>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input placeholder="请输入邮箱" v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input placeholder="请输入手机" v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="editVisible = false">取 消</el-button>
        <el-button type="primary" @click="editdata">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      query: '',
      pagenum: 1,
      pagesize: 2,
      userlist: [],
      total: 0,
      dialogVisible: false,
      editVisible: false,
      form: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      editForm: {
        username: '',
        email: '',
        mobile: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 3,
            max: 5,
            message: '长度在 3 到 5 个字符',
            trigger: ['change', 'blur']
          }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 3,
            max: 12,
            message: '长度在 3 到 12 个字符',
            trigger: ['change', 'blur']
          }
        ],
        email: [
          {
            type: 'email',
            message: '请输入正确邮箱',
            trigger: ['change', 'blur']
          }
        ],
        mobile: [
          {
            pattern: /^(0|86|17951)?(13[0-9]|15[012356789]|166|17[3678]|18[0-9]|14[57])[0-9]{8}$/,
            message: '请输入正确的手机号',
            trigger: ['change', 'blur']
          }
        ]
      }
    }
  },
  created () {
    this.getuser()
  },
  methods: {
    async getuser () {
      try {
        const { data, meta } = await this.$axios({
          method: 'get',
          url: 'users',
          params: {
            query: this.query,
            pagenum: this.pagenum,
            pagesize: this.pagesize
          }
        })

        if (meta.status === 200) {
          this.userlist = data.users
          this.total = data.total
        } else {
          this.$message.error(meta.msg)
        }
      } catch (e) {
        console.log(e)
      }
    },
    // 分页
    handleSizeChange (val) {
      this.pagesize = val
      this.pagenum = 1
      this.getuser()
    },
    handleCurrentChange (val) {
      this.pagenum = val
      this.getuser()
    },
    // 删除

    async del (id) {
      try {
        await this.$confirm('此操作将永久删除用户, 是否继续?', '警告', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        const { meta } = await this.$axios.delete(`users/${id}`)
        if (meta.status === 200) {
          this.$message.success(meta.msg)
          this.getuser()
          if (this.userlist.length === 1 && this.pagenum > 1) {
            this.pagenum--
          }
          this.getuser()
        } else {
          this.$message.error(meta.msg)
        }
      } catch (e) {
        console.log(e)
      }
    },

    seach () {
      this.pagenum = 1
      this.getuser()
    },
    async change (row) {
      const { meta } = await this.$axios.put(
        `users/${row.id}/state/${row.mg_state}`
      )

      if (meta.status === 200) {
        this.$message.success(meta.msg)
      } else {
        this.$message.error(meta.msg)
      }
    },
    adduser () {
      this.dialogVisible = true
    },
    async add () {
      try {
        await this.$refs.form.validate()
        const { meta } = await this.$axios.post('users', this.form)
        if (meta.status === 201) {
          this.$message.success(meta.msg)
          this.dialogVisible = false
          this.pagenum++
          this.pagenum = Math.ceil(this.total / this.pagesize)
          this.getuser()
        } else {
          this.$message.error(meta.msg)
          this.dialogVisible = false
        }
      } catch (e) {
        console.log(e)
      }
    },
    closeDialog () {
      this.$refs.form.resetFields()
    },
    async edit (row) {
      this.editVisible = true
      console.log(row)
      this.editForm.username = row.username
      this.editForm.email = row.email
      this.editForm.mobile = row.mobile
      this.editForm.id = row.id
    },
    async editdata () {
      try {
        await this.$refs.editForm.validate()
        const { id, email, mobile } = this.editForm

        const { meta } = await this.$axios.put(
          `users/${id}`,
          { email, mobile }
        )
        console.log(meta)
        if (meta.status === 200) {
          this.$message.success(meta.msg)
          this.editVisible = false
          this.getuser()
        } else {
          this.$message.error(meta.msg)
        }
      } catch (e) {
        console.log(e)
      }
    }
  }
}
</script>

<style lang='scss'>
.el-breadcrumb {
  margin-bottom: 10px;
}
hr {
  margin-bottom: 10px;
}
.users {
  .input-with-select {
    width: 300px;
    margin-bottom: 10px;
    margin-right: 20px;
  }
}
</style>
