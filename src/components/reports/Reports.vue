<template>
  <div>
    <!-- 面包屑区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }" @click.native="clearStorage"
        >首页</el-breadcrumb-item
      >
      <el-breadcrumb-item>数据统计</el-breadcrumb-item>
      <el-breadcrumb-item>数据报表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <div id="chart_example" style="width: 100%;height:500px;"></div>
    </el-card>
  </div>
</template>

<script>
import * as echarts from 'echarts'

export default {
  data() {
    return {
      // 需要合并的对象
      options: {
        title: {
          text: '用户来源'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#E9EEF3'
            }
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [
          {
            boundaryGap: false
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ]
      }
    }
  },
  async mounted() {
    const myChart = echarts.init(document.getElementById('chart_example'))
    const { data: res } = await this.$http.get('reports/type/1')
    if (res.meta.status !== 200) return this.$message.error('获取数据报表失败')
    // 用ES6语法合并两个对象
    const result = { ...this.options, ...res.data }
    myChart.setOption(result)
  }
}
</script>

<style></style>
