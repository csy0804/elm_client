# 饿了么前端项目（elmclient）

## 项目简介
本项目为饿了么点餐系统的前端部分，基于 Vue3 开发，包含用户注册、登录、下单、地址管理、智能推荐、个人中心等功能模块，适配移动端和PC端。

## 主要功能
- 用户注册/登录/退出
- 首页美食推荐
- 商家列表与详情
- 购物车与下单
- 订单管理与支付
- 地址管理
- 个人信息与头像修改
- 智能推荐（ChatGPT接口）

## 目录结构
```
elmclient/
├── public/           # 静态资源
├── src/
│   ├── assets/       # 图片等静态资源
│   ├── components/   # 公共组件（如Footer）
│   ├── views/        # 各页面视图（如Login、OrderList、UserAddress等）
│   ├── router/       # 路由配置
│   ├── main.js       # 入口文件
│   └── App.vue       # 根组件
├── package.json      # 项目依赖
└── README.md         # 项目说明
```

## 安装与运行
1. 安装依赖
```bash
npm install
```
2. 启动开发环境
```bash
npm run serve
```
3. 打包生产环境
```bash
npm run build
```

## 依赖环境
- Node.js 12+
- Vue 3
- Axios
- Font Awesome

## 说明
- 前端默认接口地址为 `http://localhost:14000/`，如需修改请在 `src/main.js` 中调整。
- 需配合后端 SpringCloud 服务端项目一同运行。

## 联系方式
如有问题或建议，请联系开发者。
