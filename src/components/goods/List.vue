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
      <!-- 搜索与添加区域 -->
      <el-row :gutter="20">
        <div style="margin-top: 15px;">
          <el-col :span="8">
            <el-input
              placeholder="请输入内容"
              clearable
              @clear="getGoodList"
              v-model="queryInfo.query"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                @click="getGoodList"
              ></el-button>
            </el-input>
          </el-col>
          <el-col :span="4">
            <el-button type="primary" @click="showAddpage">
              添加商品信息</el-button
            >
          </el-col>
        </div>
      </el-row>
      <!-- 表格区域  -->
      <el-table :data="goodList" border stripe>
        <!-- 索引号 -->
        <el-table-column
          type="index"
          label="#"
          width="50px"
          header-align="center"
          align="center"
        ></el-table-column>
        <!-- 商品名称 -->
        <el-table-column
          prop="goods_name"
          label="商品名称"
          header-align="left"
        ></el-table-column>
        <!-- 商品价格 -->
        <el-table-column
          prop="goods_price"
          label="商品价格(元)"
          width="95px"
          header-align="center"
          align="center"
        ></el-table-column>
        <!-- 商品重量 -->
        <el-table-column
          prop="goods_weight"
          label="商品重量"
          width="75px"
          header-align="center"
          align="center"
        >
        </el-table-column>
        <!-- 创建时间 -->
        <el-table-column
          prop="add_time"
          label="创建时间"
          width="140px"
          header-align="center"
          align="center"
        >
          <template slot-scope="scope">
            {{ scope.row.add_time | dataFormat }}</template
          >
        </el-table-column>
        <!-- 操作 -->
        <el-table-column
          label="操作"
          header-align="center"
          width="130px"
          align="center"
        >
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
              @click="showEditDialogVisible(scope.row.goods_id)"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="removeGoodById(scope.row.goods_id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 页码区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="queryInfo.pagesize"
        layout="total,sizes, prev, pager, next, jumper"
        :total="total"
        background
      >
      </el-pagination>
    </el-card>
    <!-- 删除提示框 -->
    <template>
      <el-button type="text"></el-button>
    </template>
    <!-- 修改商品数据的对话框 -->
    <el-dialog
      title="修改商品数据"
      :visible.sync="editDialogVisible"
      width="50%"
    >
      <!-- 内容主题 -->
      <el-form
        :model="editForm"
        label-position="left"
        ref="editFormRef"
        :rules="FormRules"
        label-width="80px"
      >
        <el-form-item label="商品名称" prop="goods_name">
          <el-input v-model="editForm.goods_name"></el-input>
        </el-form-item>
        <el-form-item label="商品价格" prop="goods_price">
          <el-input v-model="editForm.goods_price"></el-input>
        </el-form-item>
        <el-form-item label="商品数量" prop="goods_number">
          <el-input v-model="editForm.goods_number"></el-input>
        </el-form-item>
        <el-form-item label="商品重量" prop="goods_weight">
          <el-input v-model="editForm.goods_weight"></el-input>
        </el-form-item>
        <!-- <el-form-item label="商品介绍" prop="goods_introduce">
          <el-input v-model="editForm.goods_introduce"></el-input>
        </el-form-item>
        <el-form-item label="商品图片" prop="pics"> -->
        <!-- <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">
              只能上传jpg/png文件，且不超过500kb
            </div>
          </el-upload> -->
        <!-- </el-form-item> -->
        <!-- <el-form-item label="商品参数" prop="attrs">
          <el-input v-model="editForm.attrs"></el-input>
        </el-form-item> -->
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editGoods">
          确 定
        </el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 搜索框的输入
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 10
      },
      // 商品信息
      goodList: [],
      // 商品总数
      total: 0,
      // 修改对话框的显示与否
      editDialogVisible: false,
      // 修改的商品信息
      editForm: {},
      //
      FormRules: {
        goods_name: [
          { required: true, message: '请输入商品名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        goods_price: [
          { required: true, message: '请输入商品价格', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        goods_number: [
          { required: true, message: '请输入商品数量', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        goods_weight: [
          { required: true, message: '请输入商品重量', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        goods_introduce: [
          { required: false, message: '请输入商品简介', trigger: 'blur' },
          { min: 0, max: 50, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        pics: [
          { required: false, message: '请输入商品数量', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        attrs: [
          { required: false, message: '请输入商品数量', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ]
      }
      // 上传图片
      // fileList: [
      //   {
      //     name: 'food.jpeg',
      //     url:
      //       'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'
      //   },
      //   {
      //     name: 'food2.jpeg',
      //     url:
      //       'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'
      //   }
      // ],
    }
  },
  created() {
    // 创建页面时发送ajax请求
    this.getGoodList()
  },
  methods: {
    async getGoodList() {
      const { data: res } = await this.$http.get('goods/', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品列表失败')
      }
      this.$message.success('获取商品列表成功')
      this.goodList = res.data.goods
      this.total = res.data.total
    },
    // 监听 pagesize  5条/页 改变事件
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getGoodList()
    },
    // 监听 页码值 改变的事件
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage
      this.getGoodList()
    },
    // 展示添加商品信息对对话框
    showAddpage() {
      this.$router.push('/goods/add')
    },
    // 提交添加商品信息
    addGoods() {
      this.addDialogVisible = false
      console.log(this.addForm)
    },
    // 删除商品信息
    async removeGoodById(goodsId) {
      const confirmResult = await this.$confirm(
        '此操作将永久删除该参数, 是否继续?',
        '提示',
        {
          type: 'warning',
          confirmButtonText: '确定',
          cancelButtonText: '取消'
        }
      ).catch(err => err)
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      const { data: res } = await this.$http.delete(`goods/${goodsId}`)
      if (res.meta.status !== 200) {
        return this.$message.error('删除商品信息失败')
      }
      this.$message.success('删除成功')
      this.getGoodList()
    },
    // 显示商品修改框
    async showEditDialogVisible(goodsId) {
      this.editDialogVisible = true
      console.log(goodsId)
      const { data: res } = await this.$http.get(`goods/${goodsId}`)
      if (res.meta.status !== 200) {
        return this.$message.error('查询商品信息失败')
      }
      this.editForm = res.data
    },
    // 提交商品修改信息
    async editGoods() {
      const { data: res } = await this.$http.put(
        `goods/${this.editForm.goods_id}`,
        this.editForm
      )
      console.log(res)
      if (res.meta.status !== 200) return this.$message.error('修改失败')
      this.getGoodList()
      this.editDialogVisible = false
    }
  }
}
</script>

<style></style>
