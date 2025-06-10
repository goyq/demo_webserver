# 静态Web项目 Docker + Apache 部署指南

## 📋 项目概述

**技术栈**: 静态Web项目 + Docker + Apache + Docker Compose  
**部署环境**: Linux服务器  
**访问端口**: 8080  
**Web服务器**: Apache HTTP Server (httpd)

> **Apache特点**: 成熟稳定、模块丰富、.htaccess支持、传统Web应用友好

## 🗂️ 服务器文件结构

```
/your-project-path/
├── dist/                 # 构建输出目录
│   ├── index.html       # 主页面
│   ├── static/          # 静态资源
│   └── ...
├── Dockerfile
├── httpd.conf           # Apache配置文件
├── .htaccess           # URL重写规则（可选）
└── docker-compose.yml
```

## 🚀 部署步骤

### 1. 切换到项目目录
```bash
cd /your-project-path
```

### 2. 创建 Dockerfile
```bash
cat > Dockerfile << 'EOF'
FROM httpd:2.4-alpine

# 复制构建好的文件到Apache目录
COPY dist/ /usr/local/apache2/htdocs/

# 复制Apache配置
COPY httpd.conf /usr/local/apache2/conf/httpd.conf

# 复制.htaccess文件（如果存在）
COPY .htaccess /usr/local/apache2/htdocs/.htaccess 2>/dev/null || true

# 暴露端口
EXPOSE 8080

# 启动Apache
CMD ["httpd-foreground"]
EOF
```

### 3. 创建 httpd.conf
```bash
cat > httpd.conf << 'EOF'
# Apache HTTP Server 配置文件

ServerRoot "/usr/local/apache2"
Listen 8080

# 加载必要模块
LoadModule mpm_event_module modules/mod_mpm_event.so
LoadModule authz_core_module modules/mod_authz_core.so
LoadModule dir_module modules/mod_dir.so
LoadModule mime_module modules/mod_mime.so
LoadModule rewrite_module modules/mod_rewrite.so
LoadModule deflate_module modules/mod_deflate.so
LoadModule expires_module modules/mod_expires.so
LoadModule headers_module modules/mod_headers.so
LoadModule filter_module modules/mod_filter.so

# 基本设置
ServerName localhost:8080
DirectoryIndex index.html

# 文档根目录
DocumentRoot "/usr/local/apache2/htdocs"

# 目录权限配置
<Directory "/usr/local/apache2/htdocs">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
    
    # 启用URL重写
    RewriteEngine On
    
    # SPA路由支持 - 所有请求重定向到index.html
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.html [L]
</Directory>

# MIME类型配置
TypesConfig conf/mime.types

# 默认MIME类型
DefaultType application/octet-stream

# 启用压缩
<IfModule mod_deflate.c>
    # 压缩文本文件
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
    AddOutputFilterByType DEFLATE application/json
    
    # 不压缩已压缩的文件
    SetEnvIfNoCase Request_URI \
        \.(?:gif|jpe?g|png|zip|gz|tgz|bz2|tbz|mp3|ogg)$ no-gzip dont-vary
</IfModule>

# 静态资源缓存设置
<IfModule mod_expires.c>
    ExpiresActive On
    
    # 图片缓存1年
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
    ExpiresByType image/x-icon "access plus 1 year"
    
    # CSS/JS缓存1年
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType application/x-javascript "access plus 1 year"
    
    # 字体文件缓存1年
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/woff2 "access plus 1 year"
    ExpiresByType application/font-woff "access plus 1 year"
    ExpiresByType application/font-woff2 "access plus 1 year"
    
    # HTML文件不缓存
    ExpiresByType text/html "access plus 0 seconds"
</IfModule>

# 安全头设置
<IfModule mod_headers.c>
    # CORS设置（如果需要）
    # Header always set Access-Control-Allow-Origin "*"
    
    # 安全头
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-XSS-Protection "1; mode=block"
    
    # 缓存控制
    <FilesMatch "\.(css|js|png|jpg|jpeg|gif|ico|svg|woff|woff2)$">
        Header set Cache-Control "public, max-age=31536000, immutable"
    </FilesMatch>
</IfModule>

# 错误日志
ErrorLog /proc/self/fd/2
LogLevel warn

# 访问日志
CustomLog /proc/self/fd/1 common

# 健康检查端点
Alias /health /usr/local/apache2/htdocs/health.html

# 禁止访问敏感文件
<FilesMatch "^\.">
    Require all denied
</FilesMatch>

<Files "*.conf">
    Require all denied
</Files>
EOF
```

### 4. 创建 .htaccess（可选 - 用于SPA路由）
```bash
cat > .htaccess << 'EOF'
# Apache .htaccess 配置
# 适用于单页应用(SPA)路由

RewriteEngine On

# 处理CORS预检请求
RewriteCond %{REQUEST_METHOD} OPTIONS
RewriteRule ^(.*)$ $1 [R=200,L]

# SPA路由重写
# 如果请求的文件不存在，重定向到index.html
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-l
RewriteRule ^.*$ / [L,QSA]

# 静态资源缓存
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>

# Gzip压缩
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json
</IfModule>
EOF
```

### 5. 创建健康检查文件
```bash
cat > dist/health.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Health Check</title>
</head>
<body>
    <h1>Apache Server is Running</h1>
    <p>Status: OK</p>
    <p>Timestamp: <script>document.write(new Date().toISOString())</script></p>
</body>
</html>
EOF
```

### 6. 创建 docker-compose.yml
```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web-app:
    build: .
    container_name: apache-web-demo
    ports:
      - "8080:8080"
    restart: always
    volumes:
      - ./dist:/usr/local/apache2/htdocs:ro
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    environment:
      - APACHE_RUN_USER=www-data
      - APACHE_RUN_GROUP=www-data
EOF
```

## 🔨 容器管理命令

### 构建并启动
```bash
# 构建Docker镜像
docker-compose build

# 后台启动容器
docker-compose up -d

# 查看运行状态
docker-compose ps
```

### 查看日志
```bash
# 查看容器日志
docker-compose logs -f

# 查看Apache访问日志
docker exec apache-web-demo tail -f /usr/local/apache2/logs/access_log

# 查看Apache错误日志
docker exec apache-web-demo tail -f /usr/local/apache2/logs/error_log
```

### 重启服务
```bash
# 重启容器
docker-compose restart

# 重新加载Apache配置（无需重启）
docker exec apache-web-demo httpd -k graceful
```

## 🌐 访问地址

- **主站访问**: http://YOUR_SERVER_IP:8080
- **健康检查**: http://YOUR_SERVER_IP:8080/health
- **Apache状态**: http://YOUR_SERVER_IP:8080/server-status （需启用mod_status）

## 🛑 停止和清理

### 停止服务
```bash
# 停止容器
docker-compose down

# 停止并删除镜像
docker-compose down --rmi all
```

## 🔧 故障排查

### 检查配置语法
```bash
# 检查Apache配置语法
docker exec apache-web-demo httpd -t
```

### 查看模块状态
```bash
# 查看已加载的模块
docker exec apache-web-demo httpd -M
```

### 进入容器调试
```bash
# 进入容器
docker exec -it apache-web-demo sh

# 查看Apache进程
docker exec apache-web-demo ps aux | grep httpd
```

## ⚖️ Apache vs Nginx 对比

| 特性 | Apache | Nginx |
|------|--------|-------|
| **配置方式** | .htaccess支持 | 集中配置 |
| **模块系统** | 丰富的模块 | 精简高效 |
| **内存占用** | 相对较高 | 更低 |
| **并发处理** | 传统模型 | 事件驱动 |
| **学习曲线** | 较平缓 | 相对陡峭 |
| **适用场景** | 传统Web应用 | 高并发应用 |

## 💡 Apache特色功能

### 1. .htaccess支持
- 目录级别配置
- 无需重启服务器
- 灵活的URL重写

### 2. 模块化架构
```bash
# 启用额外模块
LoadModule ssl_module modules/mod_ssl.so
LoadModule status_module modules/mod_status.so
```

### 3. 虚拟主机
```apache
<VirtualHost *:8080>
    ServerName example.com
    DocumentRoot /usr/local/apache2/htdocs/site1
</VirtualHost>
```

## 🚀 性能优化建议

1. **启用压缩**: 已在配置中启用gzip
2. **配置缓存**: 静态资源长期缓存
3. **优化模块**: 只加载必要模块
4. **调整工作进程**: 根据服务器配置调整MPM设置 