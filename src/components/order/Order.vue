<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片区 -->
    <el-card>
      <!-- 搜索区域 -->
      <el-row :gutter="20">
        <div style="margin-top: 15px;">
          <el-col :span="8">
            <el-input
              placeholder="请输入内容"
              clearable
              v-model="queryInfo.query"
              @clear="getOrderList"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                @click="getOrderList"
              ></el-button>
            </el-input>
          </el-col>
        </div>
      </el-row>
      <!-- 订单列表区域 -->
      <el-table :data="orderList" border stripe>
        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column prop="order_number" label="订单编号"></el-table-column>
        <el-table-column
          prop="order_price"
          label="订单价格"
          width="110px"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="pay_status"
          label="是否付款"
          width="110px"
          align="center"
        >
          <template slot-scope="scope">
            <el-tag type="danger" v-if="scope.row.order_pay === '0'">
              未付款
            </el-tag>
            <el-tag type="success" v-else>已付款</el-tag>
          </template>
        </el-table-column>
        <el-table-column
          prop="is_send"
          label="是否发货"
          width="110px"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="create_time"
          label="下单时间"
          width="280px"
          align="center"
        >
          <template slot-scope="scope">
            {{ scope.row.create_time | dataFormat }}</template
          >
        </el-table-column>
        <el-table-column label="操作" width="180px" align="center">
          <template slot-scope="scope">
            <!-- 修改订单地址按钮 -->
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
              @click="showEditDialogVisible(scope.row.order_id)"
            ></el-button>
            <!-- 物流信息按钮 -->
            <el-button
              type="success"
              icon="el-icon-location"
              size="mini"
              @click="showProgressBox(scope.row.order_id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 页码区 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="queryInfo.pagesize"
        layout="total,sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination>
    </el-card>
    <!-- 修改订单地址的对话框 -->
    <el-dialog
      title="修改地址"
      :visible.sync="editaddressDialogVisible"
      width="50%"
      @close="addressDialogClosed"
    >
      <el-form
        :model="editaddressForm"
        :rules="Formrules"
        ref="editFormRef"
        label-width="100px"
      >
        <el-form-item label="省市区/县" prop="address1">
          <el-cascader
            :options="cityData"
            v-model="editaddressForm.address1"
          ></el-cascader>
        </el-form-item>
        <el-form-item label="详细地址" prop="address2">
          <el-input v-model="editaddressForm.address2"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editaddressDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editaddressDialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
    <!-- 物流进度对话框 -->
    <el-dialog
      title="物流进度"
      :visible.sync="progressBoxDialogVisible"
      width="50%"
    >
      <el-timeline>
        <el-timeline-item
          v-for="(activity, index) in progressInfo"
          :key="index"
          :timestamp="activity.time"
        >
          {{ activity.context }}
        </el-timeline-item>
      </el-timeline>
    </el-dialog>
  </div>
</template>

<script>
import cityData from './citydata'
export default {
  data() {
    return {
      // 获取用户类表的参数对象
      queryInfo: {
        query: '',
        // 当前的页数
        pagenum: 1,
        // 当前每页显示多少条数据
        pagesize: 10
      },
      // 数据总数
      total: 0,
      // 订单列表的数据
      orderList: [],
      // 修改订单地址的对话框显示与否
      editaddressDialogVisible: false,
      // 修改的订单地址的数据
      editaddressForm: {
        address1: [],
        address2: ''
      },
      Formrules: {
        address1: [
          { required: true, message: '请输选择省市县', trigger: 'blur' }
        ],
        address2: [
          { required: true, message: '请输入详细地址', trigger: 'blur' }
        ]
      },
      // 省市的数据
      cityData,
      // 物流信息对话框显示与否
      progressBoxDialogVisible: false,
      // 物流信息
      progressInfo: []
    }
  },
  created() {
    this.getOrderList()
  },
  methods: {
    // 发送请求 把订单数据存到orderList中
    async getOrderList() {
      const { data: res } = await this.$http.get('orders', {
        params: this.queryInfo
      })
      console.log(res)
      if (res.meta.status !== 200) {
        return this.$Message.error('获取订单列表数据失败')
      }
      this.orderList = res.data.goods
      this.total = res.data.total
    },
    // 监听 pagesize  5条/页 改变事件
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getOrderList()
    },
    // 监听 页码值 改变的事件
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage
      this.getOrderList()
    },
    // 展示修改订单地址的对话框
    showEditDialogVisible() {
      this.editaddressDialogVisible = true
    },
    // 监听关闭对话框
    addressDialogClosed() {
      this.$refs.editFormRef.resetFields()
    },
    // 展示修改
    async showProgressBox() {
      this.$message.error('订单数据存在异常 ')
      // 接口可能有问题
      // const { data: res } = await this.$http.get('kuaidi/1106975712662')
      // if (res.meta.status !== 200) {
      //   return this.$message.error('获取物流信息失败！')
      // }
      // console.log(res)
      // this.progressInfo = res.data

      // this.progressBoxDialogVisible = true
    }
  },
  computed: {}
}
</script>

<style lang="less" scoped></style>
