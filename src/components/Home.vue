<template>
  <div>
    <el-button type="info" @click="loginOut">退出 </el-button>
  </div>
</template>

<script>
export default {
  name: 'Home',
  methods: {
    loginOut() {
      // 清空token
      window.sessionStorage.clear()
      // 跳转登陆页面
      this.$router.push('/login')
    }
  }
}
</script>

<style lang="less"></style>
