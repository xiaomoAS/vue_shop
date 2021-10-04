<template>
    <el-container>
      <el-header style="height: auto;">
          <div>
            <img src="../assets/lianxi.png">
            <span>电商管理系统</span>
          </div>
          <el-button type="info" @click="logout">退出</el-button></el-header>
      <el-container>
        <el-aside :width="menuCollapse ? '64px' : '200px'">
          <div class="toggle_button" @click="toggleCollapse">|||</div>
          <el-menu background-color="#333744" text-color="#fff" active-text-color="#409EFF" unique-opened :collapse="menuCollapse" :collapse-transition="false" router :default-active="navState">
            <el-submenu :index="index + ''" v-for="(item, index) in menuList" :key="item.id">
              <template slot="title">
                <i :class="iconsObj[item.id]"></i>
                <span>{{item.authName}}</span>
              </template>
              <el-menu-item :index="'/' + subitem.path" v-for="subitem in item.children" :key="subitem.id" @click="saveNavState('/' + subitem.path)">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>{{subitem.authName}}</span>
                </template>
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
            <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      menuList: [],
      iconsObj: {
        125: 'el-icon-user-solid',
        103: 'el-icon-s-cooperation',
        101: 'el-icon-s-goods',
        102: 'el-icon-s-order',
        145: 'el-icon-s-platform'
      },
      menuCollapse: false,
      navState: window.sessionStorage.getItem('navState')
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      const { data: result } = await this.$http.get('menus')
      if (result.meta.status === 200) {
        this.menuList = result.data
      } else {
        this.$message.error(result.meta.msg)
      }
    },
    toggleCollapse () {
      this.menuCollapse = !this.menuCollapse
    },
    saveNavState (state) {
      this.navState = state
      window.sessionStorage.setItem('navState', state)
    }
  }
}
</script>

<style scoped>
.el-container {
  height: 100%;
}

.el-header {
    background-color: #373d41;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.el-header div {
    display: flex;
    align-items: center;
}

.el-header div span{
    font-size: 20px;
    color: white;
    margin-left: 15px;
}

.el-aside {
    background-color: #333744;
}

.el-main {
    background-color: #eaedf1;
}

.el-menu {
    border-right: none;
}

.el-menu i {
    margin-right: 10px;
}

.toggle_button {
    background-color: #4a5064;
    font-size: 16px;
    line-height: 24px;
    text-align: center;
    letter-spacing: 0.2em;
    cursor: pointer;
    color: white;
}
</style>
