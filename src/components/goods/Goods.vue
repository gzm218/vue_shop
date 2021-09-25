<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片试图区域  -->
    <el-card>
      <!--添加按钮  -->
      <el-row>
        <el-col>
          <el-button type="primary" @click="addDialogVisible = true">
            添加分类
          </el-button>
        </el-col>
      </el-row>

      <!-- 表格区域  -->

      <!-- 页码区域 -->
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {}
  }
}
</script>

<style></style>
