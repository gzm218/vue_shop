# vue_shop

### 1 Project setup

```
npm install
```

### 2 Compiles and hot-reloads for development

```
npm run serve
```

### 3 Compiles and minifies for production

```
npm run build
```

### 4 Lints and fixes files

```
npm run lint
```

### 5 Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).

### 6 登陆业务相关的技术点

- http 是无状态的
- 通过 cookie 在客户端记录状态
- 通过 session 在服务器端记录状态
- 通过 token 方式维持状态 （存在跨域需要

### 7 登陆-token 原理分析

- 登陆页面输入用户名和密码进行登陆
- 服务器验证通过之后生成该用户的 token 并返回
- 客户端存储该 token
- 后续所有的请求都要携带该 token 发送请求

### 8 Git 操作

- git branch 查看本地所有分支
- git status 查看当前状态
  git commit 提交
  git branch -a 查看所有的分支
  git branch -r 查看远程所有分支
  git commit -am "init" 提交并且加注释
  git remote add origin git@192.168.1.119:ndshow
  git push origin master 将文件给推到服务器上
  git remote show origin 显示远程库 origin 里的资源
  git push origin master:develop
  git push origin master:hb-dev 将本地库与服务器上的库进行关联
  git checkout --track origin/dev 切换到远程 dev 分支
  git branch -D master develop 删除本地库 develop
  git checkout -b dev 建立一个新的本地分支 dev
  git merge origin/dev 将分支 dev 与当前分支进行合并
  git checkout dev 切换到本地 dev 分支
  git remote show 查看远程库
  git add .
  git rm 文件名(包括路径) 从 git 中删除指定文件
  git clone git://github.com/schacon/grit.git 从服务器上将代码给拉下来
  git config --list 看所有用户
  git ls-files 看已经被提交的
  git rm [file name] 删除一个文件
  git commit -a 提交当前 repos 的所有的改变
  git add [file name] 添加一个文件到 git index
  git commit -v 当你用－v 参数的时候可以看 commit 的差异
  git commit -m "This is the message describing the commit" 添加 commit 信息
  git commit -a -a 是代表 add，把所有的 change 加到 git index 里然后再 commit
  git commit -a -v 一般提交命令
  git log 看你 commit 的日志
  git diff 查看尚未暂存的更新
  git rm a.a 移除文件(从暂存区和工作区中删除)
  git rm --cached a.a 移除文件(只从暂存区中删除)
  git commit -m "remove" 移除文件(从 Git 中删除)
  git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)
  git diff --cached 或 \$ git diff --staged 查看尚未提交的更新
  git stash push 将文件给 push 到一个临时空间中
  git stash pop 将文件从临时空间 pop 下来
  git remote add origin git@github.com:username/Hello-World.git
  git push origin master 将本地项目给提交到服务器中

### 9 git pull 本地与服务器端同步

git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。
git push origin serverfix:awesomebranch
git fetch 相当于是从远程获取最新版本到本地，不会自动 merge
git commit -a -m "log_message" (-a 是提交所有改动，-m 是加入 log 信息) 本地修改同步至服务器端 ：
git branch branch_0.1 master 从主分支 master 创建 branch_0.1 分支
git branch -m branch_0.1 branch_1.0 将 branch_0.1 重命名为 branch_1.0
git checkout branch_1.0/master 切换到 branch_1.0/master 分支
du -hs
git branch 删除远程 branch
git push origin :branch_remote_name
git branch -r -d branch_remote_name

# problem

### 1 问题

- 用户搜索的关键字是 服务器写好的
- 解构赋值还需要在了解 Home.vue 里 获取所有菜单
- Login 中的 el-form-item 中 prop 在理解一下 好像是默认值（9.16 理解 （9.17 是 el-form-item 中用于验证表单
- 侧边栏的高亮设置 通过动态的 activePath 储存在 sessionStorage 中 代码再去看
- ref 的作用还要去了解
- Rights 组件中,分配权限用到的一个递归函数 花时间再去理解 试试看有没有其他的方法
- 所有的 API 一定要用对，数据要一一对应不要看岔了然后得不到数据，找不到数一般都是数据名写错了
- 考虑一下是否有些代码可以复原 减少重复
- 级联选择器 稍微复杂 要多去理解它的操作
- Add 中的 getCateList()与 Params 中的应该可以复用 10.1 完成后再尝试看看
- 深拷贝
- 商品列表如果有修改 图片 介绍 的要求要如何区实现
- 遇到不同状态的标签的显示要用到 v-if v-else 如果是标签的属性需要动态绑定才是要：xxx="变化的属性"
- 加载懒路由时不用设置分组 不然会报错
- 因为 cdn 引入较低版本的 elment 所以级联选择器需要点击
- 报错 rowKey 是因为版本问题 cdn 的版本和 package.json 里要一致

### 2 笔记

- 左侧导航栏渲染页面 通过嵌套循环 v-for ：key 同时 菜单栏的 index 也要发生动态变化 否则会引起所有下拉框同时行动，可以与 key 值相同,index 值是字符串 +''就行
- 关于侧边栏的动态变化 v-bind 突出重要作用
- 布局页面时 Element 组件库中的属性也很重要 例如侧边栏的 router 可以通过属性去设置改造路由链接
- 验证表单通过正则表达式 表单验证可以只只有一个规则里面可以包含多个属性的验证规则
- 表单关闭后的重置，通过监听对话框关闭的时间 @close 去重置
- 树形解构的属性绑定对象 Props label 是指定节点标签为节点对象的某个属性值，children
- 分配权限思路 在点击分配时就把角色的 id 保存到数据中，因为请求要传角色 id 然后通过树形的两个方法，把半选和全选的 id 只拼接成一个数组然后发送到服务器端，然后成功后重新渲染页面
- 商品分类引入了 树形表格 多个参数 有的需要用到插槽
- 定义一个全局全局的过滤器 调用时间函数 返回一个时间格式 方法 psdstart(位数，补全的内容)
-

### 3 项目优化策略

- 生成打包报告
- 第三方库启用 CDN
- Element-UI 组件按需加载
- 路由懒加载
- 首页内容定制
- configureWebpack 和 chinWebpack
  - configureWebpack 通过操作对象的形式，来修改默认的 webpack 配置
  - chinWebpack 通过链式编程的形式，来修改没默认的 webpack 配置
