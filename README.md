# vue_shop

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).

### 登陆业务相关的技术点

- http 是无状态的
- 通过 cookie 在客户端记录状态
- 通过 session 在服务器端记录状态
- 通过 token 方式维持状态 （存在跨域需要

### 登陆-token 原理分析

- 登陆页面输入用户名和密码进行登陆
- 服务器验证通过之后生成该用户的 token 并返回
- 客户端存储该 token
- 后续所有的请求都要携带该 token 发送请求
