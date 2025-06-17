# uni-app 示例项目
一个简单的uni-app示例项目，实现用户输入名字后显示问候语的功能。

## uni-app 项目创造
   -[uni-app项目创建总结.md](https://github.com/goyq/demo_webserver/blob/main/uni-app%E9%A1%B9%E7%9B%AE%E5%88%9B%E5%BB%BA%E6%80%BB%E7%BB%93.md)


## 项目结构

```
demo_webserver
├── pages/
│   └── index.vue        # uni-app主页面（完整Vue组件）
├── App.vue              # uni-app应用根组件
├── main.js              # uni-app应用入口文件
├── manifest.json        # uni-app应用配置文件
├── pages.json           # uni-app页面路由配置
├── index.html           # 静态部署版本（完整HTML应用）
├── package.json         # 项目配置（简化脚本）
├── package-lock.json    # 依赖锁定文件
└── node_modules/        # 依赖包目录
```

## 快速开始

### 方式一：命令行运行（推荐）

```bash
# 安装依赖（如果需要）
npm install

# 启动H5开发服务器
npm run dev:h5

# 其他可用命令
npm run serve      # 等同于dev:h5
npm run help       # 查看命令说明
```

## 🚀 部署教程

### 使用[npm run build:h5]命令构建项目

### 方式一：Docker+Nginx部署
   -[Docker+Nginx部署指南.md](https://github.com/goyq/demo_webserver/blob/main/Docker%2BNginx%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97.md)
   -[静态Web项目 Docker + Nginx 通用部署模板](https://github.com/goyq/demo_webserver/blob/main/Docker%2BNginx%E9%80%9A%E7%94%A8%E9%83%A8%E7%BD%B2%E6%A8%A1%E6%9D%BF.md)

   
### 方式二：Docker+Apache部署
   -[Docker+Apache部署指南.md](https://github.com/goyq/demo_webserver/blob/main/Docker%2BApache%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97.md)

<br />
<br />


## License
CC BY-NC 4.0 ---updated
