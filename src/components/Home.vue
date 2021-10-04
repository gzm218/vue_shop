<template>
  <el-container class="home-container">
    <!-- 头部区域 -->
    <el-header>
      <div class="header-left">
        <img src="../assets/gzm.jpg" alt="" />
        <span>信息后台管理系统</span>
      </div>
      <div class="header-right">
        <el-button type="info" @click="loginOut">退出 </el-button>
      </div>
    </el-header>
    <!-- 页面主体区域 -->
    <el-container>
      <!-- 侧边栏区域 -->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="fold">|||</div>
        <!-- 侧边栏菜单区 -->
        <el-menu
          background-color="#333744"
          text-color="#fff"
          active-text-color="#0078d4"
          unique-opened·
          :collapse="isCollapse"
          :collapse-transition="false"
          router
          :default-active="activePath"
          unique-opened
        >
          <!-- 一级菜单  用户管理-->
          <el-submenu
            :index="item.id + ''"
            v-for="item in menuList"
            :key="item.id"
          >
            <!-- 一级菜单模板区域 -->
            <template slot="title">
              <!-- 图标 -->
              <i :class="iconsObj[item.id]"></i>
              <!-- 一级菜单名 -->
              <span>{{ item.authName }}</span>
            </template>
            <!-- 二级菜单   index 作为跳转地址-->
            <el-menu-item
              :index="'/' + secItem.path"
              v-for="secItem in item.children"
              :key="secItem.id"
              @click="saveNavState('/' + secItem.path)"
            >
              <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-menu"></i>
                <span>{{ secItem.authName }}</span>
              </template></el-menu-item
            >
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 右侧区域 -->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      // 左侧菜单数据
      menuList: [],
      iconsObj: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      // 是否折叠
      isCollapse: false,
      // 被激活的动态地址
      activePath: ''
    }
  },
  // 组件创建时就获取菜单
  created() {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    loginOut() {
      // 清空token
      window.sessionStorage.clear()
      // 跳转登陆页面
      this.$router.push('/login')
    },
    // 获取所有的菜单
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
      // console.log(res)
    },
    // 点击按钮菜单折叠
    fold() {
      this.isCollapse = !this.isCollapse
    },
    // 保存链接的激活状态
    saveNavState(activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
}
// 头部样式
.el-header {
  background-color: #363d40;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 24px;
  > div {
    display: flex;
    align-items: center;
    span {
      padding-left: 15px;
    }
  }
  img {
    width: 6%;
    border-radius: 50%;
  }
}
// 侧边栏样式
.el-aside {
  background-color: #313743;
  .el-menu {
    border-right: none;
  }
}
// 右边样式
.el-main {
  background-color: #e9edf1;
}
// icon字体图片样式
.iconfont {
  margin-right: 10px;
}
.toggle-button {
  background-color: #475163;
  color: #fff;
  font-size: 10px;
  line-height: 24px;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
