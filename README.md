# 🎉 uni-app 示例项目

一个简单的uni-app示例项目，实现用户输入名字后显示问候语的功能。

## 📱 功能特性

- 用户输入姓名
- 点击按钮显示个性化问候
- 响应式设计，支持多端运行
- 现代化UI设计，包含动画效果

## 🚀 快速开始

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

### 方式二：HBuilderX运行（完整功能）

1. **下载HBuilderX**
   - 访问 [HBuilderX官网](https://www.dcloud.io/hbuilderx.html)
   - 下载并安装HBuilderX

2. **导入项目**
   - 打开HBuilderX
   - 点击"文件" → "导入" → "从本地目录导入"
   - 选择项目文件夹

3. **运行项目**
   - 点击工具栏的"运行"按钮
   - 选择运行到：浏览器（H5）、微信开发者工具（小程序）、模拟器或真机（App）

## 🏗️ 项目结构

```
my-hello-app/
├── pages/               # 页面文件
│   └── index.vue       # 首页
├── App.vue             # 应用根组件
├── main.js             # 应用入口文件
├── manifest.json       # 应用配置文件
├── pages.json          # 页面路由配置
├── package.json        # 项目依赖配置
├── package-lock.json   # 依赖锁定文件
├── node_modules/       # 依赖包目录
└── README.md           # 项目说明
```

## 🚀 部署教程

### 🌐 H5网页部署（推荐新手）

#### 方案一：使用当前HTTP服务器部署
```bash
# 1. 确保项目依赖已安装
npm install

# 2. 启动HTTP服务器（本地预览）
npm run dev:h5

# 3. 访问 http://localhost:8081 查看效果
```

#### 方案二：静态文件部署
1. **准备静态文件**
   - 将项目根目录的所有文件复制到Web服务器目录
   - 确保 `pages/index.vue`、`App.vue` 等文件完整

2. **部署到常见平台**

   **GitHub Pages部署：**
   ```bash
   # 1. 创建GitHub仓库
   git init
   git add .
   git commit -m "初始化uni-app项目"
   git branch -M main
   git remote add origin https://github.com/你的用户名/你的仓库名.git
   git push -u origin main
   
   # 2. 在GitHub仓库设置中启用Pages，选择main分支
   # 3. 访问 https://你的用户名.github.io/你的仓库名
   ```

   **Vercel部署：**
   ```bash
   # 1. 安装Vercel CLI
   npm install -g vercel
   
   # 2. 登录并部署
   vercel login
   vercel --prod
   
   # 3. 按提示操作，获得部署链接
   ```

   **Netlify部署：**
   - 访问 https://netlify.com
   - 拖拽项目文件夹到部署区域
   - 获得自动生成的网址

#### 方案三：使用HBuilderX构建H5版本
1. **安装HBuilderX**
   - 下载：https://www.dcloud.io/hbuilderx.html
   - 导入当前项目文件夹

2. **构建H5项目**
   ```
   HBuilderX → 发行 → 网站-PC Web或手机H5
   ├── 选择发行目录
   ├── 配置基础路径（如果需要）
   └── 点击"发行"按钮
   ```

3. **部署生成的文件**
   - 找到 `dist/build/h5` 目录
   - 将目录内所有文件上传到Web服务器
   - 配置服务器支持单页应用（SPA）

### 📱 小程序部署

#### 微信小程序部署

1. **准备工作**
   ```bash
   # 注册微信小程序
   # 1. 访问 https://mp.weixin.qq.com/
   # 2. 注册小程序账号
   # 3. 获取AppID
   ```

2. **配置项目**
   - 编辑 `manifest.json` 文件：
   ```json
   {
     "mp-weixin": {
       "appid": "你的小程序AppID",
       "setting": {
         "urlCheck": false
       }
     }
   }
   ```

3. **使用HBuilderX发布**
   ```
   HBuilderX → 发行 → 小程序-微信
   ├── 填写小程序名称
   ├── 输入AppID
   └── 点击"发行"
   ```

4. **微信开发者工具发布**
   ```bash
   # 1. 下载微信开发者工具
   # 2. 打开工具，导入项目
   # 3. 选择 dist/build/mp-weixin 目录
   # 4. 填入AppID
   # 5. 预览测试
   # 6. 上传代码
   # 7. 提交审核
   ```

#### 支付宝小程序部署

1. **准备工作**
   - 注册支付宝小程序：https://mini.open.alipay.com/
   - 获取AppID

2. **配置和发布**
   ```
   HBuilderX → 发行 → 小程序-支付宝
   ├── 配置AppID
   ├── 选择发行目录
   └── 使用支付宝开发者工具打开 dist/build/mp-alipay
   ```

### 📲 App应用部署

#### Android APK打包

1. **云端打包（推荐）**
   ```
   HBuilderX → 发行 → 原生App-云端打包
   ├── 选择Android
   ├── 填写应用信息
   │   ├── 应用名称
   │   ├── 应用版本号
   │   └── 应用图标
   ├── 选择证书（可使用公共证书测试）
   └── 提交打包
   ```

2. **本地打包**
   ```bash
   # 需要Android开发环境
   # 1. 安装Android Studio
   # 2. 配置SDK和NDK
   # 3. 在HBuilderX中配置本地打包环境
   ```

#### iOS应用打包

1. **云端打包**
   ```
   HBuilderX → 发行 → 原生App-云端打包
   ├── 选择iOS
   ├── 需要Apple开发者证书
   ├── 配置证书和描述文件
   └── 提交打包
   ```

### 🔧 部署优化建议

#### 性能优化
1. **图片优化**
   ```bash
   # 压缩图片大小
   # 使用webp格式（支持的浏览器）
   # 使用CDN加速图片加载
   ```

2. **代码优化**
   ```bash
   # 删除console.log
   # 启用代码压缩
   # 使用生产环境构建
   ```

#### 服务器配置
1. **Apache配置**
   ```apache
   # .htaccess 文件
   RewriteEngine On
   RewriteRule ^(?!.*\.).*$ /index.html [L]
   ```

2. **Nginx配置**
   ```nginx
   location / {
     try_files $uri $uri/ /index.html;
   }
   ```

### 📞 部署问题排查

#### 常见问题：
1. **页面空白** - 检查路径配置，确保静态资源正确加载
2. **404错误** - 配置服务器支持单页应用路由
3. **跨域问题** - 配置CORS或使用代理服务器
4. **小程序审核失败** - 检查内容合规性，完善隐私政策

#### 调试技巧：
```bash
# 查看控制台错误信息
# 检查网络请求状态
# 使用开发者工具调试
```

## 🎨 界面预览

该项目包含以下特性：
- 🎨 渐变背景设计
- 💫 输入框和按钮的现代化样式
- 🎯 交互式反馈（禁用状态、点击效果）
- 📱 响应式布局，适配不同设备
- ✨ 平滑的动画过渡效果

## 🔧 自定义配置

### 修改应用信息
编辑 `manifest.json` 文件中的以下字段：
- `name`: 应用名称
- `appid`: 应用ID
- `description`: 应用描述

### 修改页面配置
编辑 `pages.json` 文件可以：
- 添加新页面
- 修改导航栏样式
- 配置页面权限等

## 📝 开发说明

1. **添加新页面**: 在 `pages` 目录下创建新的 `.vue` 文件，并在 `pages.json` 中注册
2. **全局样式**: 在 `App.vue` 中的 `<style>` 标签内添加全局样式
3. **组件开发**: 可以在 `components` 目录下创建可复用组件
4. **状态管理**: 对于复杂应用，建议使用 Vuex 进行状态管理

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## �� 许可证

MIT License 