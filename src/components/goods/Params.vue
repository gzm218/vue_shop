<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>分类参数</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <!-- 警示标签 -->
      <el-alert
        title="注意：只允许为第三级分类设置相关参数"
        type="warning"
        show-icon
        :closable="false"
      >
      </el-alert>
      <!-- 级联选择器 -->
      <el-row class="cat_opt">
        <el-col>
          <span
            >选择商品的分类:
            <el-cascader
              v-model="selectedCateKeys"
              :options="CateList"
              :props="cateProps"
              @change="parentCateChange"
              clearable
            ></el-cascader>
          </span>
        </el-col>
      </el-row>
      <!-- tabs 页签区 -->
      <el-tabs v-model="activeName" @tab-click="handleTabClick">
        <el-tab-pane label="动态参数" name="many">
          <el-button
            type="primary"
            :disabled="isAble"
            size="mini"
            @click="addDialogVisible = true"
            >添加参数</el-button
          >
          <!-- 动态参数表格 -->
          <el-table :data="manyParamsDate" border stripe>
            <!-- 展开 -->
            <el-table-column type="expand">
              <template slot-scope="scope">
                <el-tag
                  v-for="(item, i) in scope.row.attr_vals"
                  :key="i"
                  closable
                  @close="handleClose(i, scope.row)"
                >
                  {{ item }}
                </el-tag>
                <!-- 输入的文本框 -->
                <el-input
                  class="input-new-tag"
                  v-if="scope.row.inputVisible"
                  v-model="scope.row.inputValue"
                  ref="saveTagInput"
                  size="small"
                  @keyup.enter.native="handleInputConfirm(scope.row)"
                  @blur="handleInputConfirm(scope.row)"
                >
                </el-input>
                <el-button
                  v-else
                  class="button-new-tag"
                  size="small"
                  @click="showInput(scope.row)"
                  >+ New Tag</el-button
                >
              </template>
            </el-table-column>
            <!-- 索引列 -->
            <el-table-column type="index" label="#"></el-table-column>
            <!-- 参数名称 -->
            <el-table-column
              label="参数名称"
              prop="attr_name"
            ></el-table-column>
            <!-- 操作 -->
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button
                  type="primary"
                  icon="el-icon-edit"
                  size="mini"
                  @click="showEditDialogVisible(scope.row.attr_id)"
                  >修改</el-button
                >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  @click="removeParamsById(scope.row.attr_id)"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="静态属性" name="only">
          <el-button
            type="primary"
            :disabled="isAble"
            size="mini"
            @click="addDialogVisible = true"
            >添加属性</el-button
          >
          <!-- 静态参数表格 -->
          <el-table :data="onlyParamsDate" border stripe>
            <!-- 展开 -->
            <el-table-column type="expand" class="expand">
              <template slot-scope="scope">
                <!-- 循环渲染的Tag框  -->
                <el-tag
                  v-for="(item, i) in scope.row.attr_vals"
                  :key="i"
                  closable
                  @close="handleClose(i, scope.row)"
                >
                  {{ item }}
                </el-tag>
                <!-- 输入的文本框 -->
                <el-input
                  class="input-new-tag"
                  v-if="scope.row.inputVisible"
                  v-model="scope.row.inputValue"
                  ref="saveTagInput"
                  size="small"
                  @keyup.enter.native="handleInputConfirm(scope.row)"
                  @blur="handleInputConfirm(scope.row)"
                >
                </el-input>
                <el-button
                  v-else
                  class="button-new-tag"
                  size="small"
                  @click="showInput(scope.row)"
                  >+ New Tag</el-button
                >
              </template>
            </el-table-column>
            <!-- 索引列 -->
            <el-table-column type="index" label="#"></el-table-column>
            <!-- 参数名称 -->
            <el-table-column
              label="属性名称"
              prop="attr_name"
            ></el-table-column>
            <!-- 操作 -->
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button
                  type="primary"
                  icon="el-icon-edit"
                  size="mini"
                  @click="showEditDialogVisible(scope.row.attr_id)"
                  >修改</el-button
                >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  @click="removeParamsById(scope.row.attr_id)"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
      </el-tabs>
    </el-card>
    <!-- 添加静态或动态参数的对话框 -->
    <el-dialog
      :title="'添加' + titleText"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="addDialogClosed"
    >
      <!-- 内容主题 -->
      <el-form
        :model="addForm"
        label-position="left"
        ref="addFormRef"
        :rules="FormRules"
        label-width="80px"
      >
        <el-form-item :label="titleText" prop="attr_name">
          <el-input v-model="addForm.attr_name"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addParmas">
          确 定
        </el-button>
      </span>
    </el-dialog>
    <!-- 修改静态或动态参数的对话框 -->
    <el-dialog
      :title="'修改' + titleText"
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
        <el-form-item :label="titleText" prop="attr_name">
          <el-input v-model="editForm.attr_name"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editParmas">
          确 定
        </el-button>
      </span>
    </el-dialog>
    <!-- 删除提示框 -->
    <template>
      <el-button type="text"></el-button>
    </template>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 获取的商品分类信息
      CateList: [],
      // 用来指定级联选择器的配置对象
      cateProps: {
        value: 'cat_id',
        label: 'cat_name',
        children: 'children',
        expandTrigger: 'hover'
      },
      // 选中的父级分类的数组
      selectedCateKeys: [],
      // 被激活的页签的名称  通过 only 或 many 来获取分类静态参数还是动态参数
      activeName: 'many',
      // 储存静态参数
      onlyParamsDate: [],
      // 储存动态参数
      manyParamsDate: [],
      // 添加的静态参数
      addForm: {
        attr_name: ''
      },
      // 检测添加静态属性的对话框显示与否
      addDialogVisible: false,
      FormRules: {
        attr_name: [
          { required: true, message: '请输入属性名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ]
      },
      // 修改对话框的显示与否
      editDialogVisible: false,
      // 修改的内容
      editForm: {
        attr_name: ''
      }
    }
  },
  created() {
    this.getCateList()
  },
  methods: {
    // 获取商品分类信息
    async getCateList() {
      const { data: res } = await this.$http.get('categories')
      if (res.meta.status !== 200) {
        return this.$message.error('获取分类信息失败')
      }
      this.CateList = res.data
    },
    // 选中项发生变化触发这个函数
    parentCateChange() {
      this.getParmasDate()
    },
    // tab页签点击事情
    handleTabClick() {
      this.getParmasDate()
    },
    // 获取参数信息
    async getParmasDate() {
      // 如果 selectedKeys 数组中的 length 大于0，证明选中的父级分类
      // 反之，就说明选中的不是三级分类
      if (this.selectedCateKeys.length !== 3) {
        // 父级分类的id发生变化
        this.selectedCateKeys = []
        this.onlyParamsDate = []
        this.manyParamsDate = []
        return
      }
      // 根据选中的分类的id，和当前所处的面板，获取对应的参数
      const { data: res } = await this.$http.get(
        `categories/${this.cateId}/attributes`,
        {
          params: { sel: this.activeName }
        }
      )
      if (res.meta.status !== 200) {
        return this.$message.error('获取参数列表失败')
      }
      res.data.forEach(item => {
        item.attr_vals = item.attr_vals ? item.attr_vals.split(' ') : []
        // 控制按钮与文本框的切换显示
        item.inputVisible = false
        item.inputValue = ''
      })
      console.log(res.data)
      if (this.activeName === 'many') {
        this.manyParamsDate = res.data
      } else {
        this.onlyParamsDate = res.data
      }
    },
    // 添加动态静态参数
    addParmas() {
      console.log(this.addForm)
      console.log(this.selectedCateKeys)
      console.log(this.activeName)
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post(
          `categories/${this.cateId}/attributes`,
          {
            attr_name: this.addForm.attr_name,
            attr_sel: this.activeName
          }
        )
        if (res.meta.status !== 201) return this.$message.error('添加失败')
        this.$message.success('添加成功')
        this.addDialogVisible = false
        this.getParmasDate()
      })
    },
    // 监听对话框的关闭事件
    addDialogClosed() {
      this.$refs.addFormRef.resetFields()
    },
    // 点击展示修改对话框 并获取当前参数id
    async showEditDialogVisible(attrId) {
      this.editDialogVisible = true
      // 方法2 可以从插槽中 获取整个参数的值 既可以得到id 又可以得到attr_id
      // 可以直接这样子没必要再去发送ajax请求 9.29理解
      // this.editForm.attr_name = params.attr_name
      // console.log(params)
      // 查询当前参数的数值
      const { data: res } = await this.$http.get(
        `categories/${this.cateId}/attributes/${attrId}`,
        {
          attr_sel: this.activeName
        }
      )
      if (res.meta.status !== 200) return this.$message.error('查询参数失败')
      this.editForm = res.data
    },
    // 监听对话框的关闭事件
    editDialogClosed() {
      this.$refs.editFormRef.resetFields()
    },
    // 提交编辑信息重新渲染页面
    async editParmas() {
      this.$refs.editFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.put(
          `categories/${this.cateId}/attributes/${this.editForm.attr_id}`,
          {
            attr_name: this.editForm.attr_name,
            attr_sel: this.activeName
          }
        )
        // console.log(res)
        if (res.meta.status !== 200) return this.$message.error('修改数据失败')
        this.$message.success('修改成功')
        this.getParmasDate()
        this.editDialogVisible = false
      })
    },
    // 根据id删除对应信息
    async removeParamsById(attrId) {
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
      const { data: res } = await this.$http.delete(
        `categories/${this.cateId}/attributes/${attrId}`
      )
      if (res.meta.status !== 200) {
        return this.$message.error('删除失败')
      }
      this.$message.success('删除成功')
      this.getParmasDate()
    },
    // 文本框失去焦点或者按下enter键触发
    handleInputConfirm(row) {
      if (row.inputValue.trim().length === 0) {
        row.inputValue = ''
        row.inputVisible = false
        return
      }
      // 反之证明有输入内容 继续下一步操作
      row.attr_vals.push(row.inputValue.trim())
      row.inputValue = ''
      row.inputVisible = false
      this.saveAttrVals(row)
    },
    async saveAttrVals(row) {
      // 需要发起请求 存储到数据库中
      const { data: res } = await this.$http.put(
        `categories/${this.cateId}/attributes/${row.attr_id}`,
        {
          attr_name: row.attr_name,
          attr_sel: row.attr_sel,
          attr_vals: row.attr_vals.join(' ')
        }
      )
      if (res.meta.status !== 200) return this.$message.error('修改参数失败')
      this.$message.success('修改参数成功')
    },
    // 点击按钮展示输入文本框
    showInput(row) {
      row.inputVisible = true
      // $nextTick 方法的作用，就是当页面上元素被重新渲染之后，才会指定回调函数中的代码
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },
    // 删除标签
    handleClose(i, row) {
      row.attr_vals.splice(i, 1)
      this.saveAttrVals(row)
    }
  },
  // 计算属性
  computed: {
    // 用来返回 是否禁用按钮的 布尔值
    isAble() {
      if (this.selectedCateKeys.length !== 3) {
        return true
      } else {
        return false
      }
    },
    // 当前选中的三级分类的id
    cateId() {
      if (this.selectedCateKeys.length === 3) {
        return this.selectedCateKeys[2]
      }
      return null
    },
    // 动态计算标题的文本
    titleText() {
      if (this.activeName === 'many') {
        return '动态参数'
      }
      return '静态属性'
    }
  }
}
</script>

<style lang="less" scoped>
.cat_opt {
  margin: 15px;
}
.el-tag {
  margin: 10px;
}
.input-new-tag {
  width: 120px;
}
.expand {
  margin-left: 15px;
}
</style>
