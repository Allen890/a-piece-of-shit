<template>
  <el-container class="index">
    <el-header>
      <div class="img">
        <img src="../assets/logo.png" alt />
      </div>

      <div class="system">
        <h1>电商后台管理系统</h1>
      </div>

      <div class="logout">
        <span>
          欢迎光临~
          <a href="javascript:;" @click="logout">退出</a>
        </span>
      </div>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          @open="handleOpen"
          @close="handleClose"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <el-submenu index="1">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>用户管理</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="1-1">用户列表</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="2">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>权限管理</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="2-1">角色列表</el-menu-item>
            </el-menu-item-group>
            <el-menu-item-group>
              <el-menu-item index="2-2">权限列表</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  methods: {
    logout () {
      this.$confirm('亲,你确定要退出系统吗？', '温馨提示', {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          localStorage.removeItem('token')
          this.$message({
            type: 'success',
            message: '退出成功!'
          })
          this.$router.push('/login')
        })
        .catch(() => {})
    }
  }
}
</script>

<style lang='scss' scoped>
.index {
  height: 100%;
  .el-header {
    background-color: #d8d8d8;
    display: flex;
    .img {
      width: 200px;
      img {
        height: 40px;
        margin: 10px;
      }
    }
    .system {
      flex: 1;
      text-align: center;
      line-height: 60px;
      color: #545c64;
    }
    .logout {
      width: 200px;
      font-weight: 700;
      line-height: 60px;
      text-align: right;
      a {
        color: orange;
      }
    }
  }
  .el-aside {
    background-color: #545c64;
  }
  .el-main {
    background-color: #ecf0f1;
  }
}
</style>
