# 静态Web项目 Docker + Nginx 通用部署模板

## 📋 配置说明

**技术栈**: 静态Web项目 + Docker + Nginx + Docker Compose  
**部署环境**: Linux服务器  
**建议端口**: 80/443 (生产) 或 8080 (测试)

> **使用前请根据实际项目调整相关配置**

## 🗂️ 通用文件结构

```
/your-project-path/
├── dist/                 # 构建输出目录（可能是build、public等）
│   ├── index.html       # 主页面
│   ├── static/          # 静态资源
│   └── ...
├── Dockerfile
├── nginx.conf
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
FROM nginx:alpine

# 复制构建好的文件到nginx目录
# 注意：根据项目调整源路径 (dist/、build/、public/ 等)
COPY dist/ /usr/share/nginx/html/

# 复制nginx配置
COPY nginx.conf /etc/nginx/nginx.conf

# 暴露端口 (根据需要调整)
EXPOSE 8080

# 启动nginx
CMD ["nginx", "-g", "daemon off;"]
EOF
```

### 3. 创建 nginx.conf（基础模板）
```bash
cat > nginx.conf << 'EOF'
events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    
    sendfile        on;
    keepalive_timeout  65;
    
    # 启用gzip压缩
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;

    server {
        listen       8080;  # 调整端口
        server_name  localhost;  # 调整域名
        
        # 主页面配置
        location / {
            root   /usr/share/nginx/html;
            index  index.html;
            try_files $uri $uri/ /index.html;  # SPA路由支持
        }
        
        # 静态资源缓存策略
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
            root   /usr/share/nginx/html;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
        
        # API代理（如果需要）
        # location /api/ {
        #     proxy_pass http://backend-service:3000/;
        #     proxy_set_header Host $host;
        #     proxy_set_header X-Real-IP $remote_addr;
        # }
        
        # 健康检查
        location /health {
            access_log off;
            return 200 "healthy\n";
            add_header Content-Type text/plain;
        }
    }
}
EOF
```

### 4. 创建 docker-compose.yml
```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web-app:
    build: .
    container_name: your-app-name  # 修改容器名
    ports:
      - "8080:8080"  # 调整端口映射
    restart: always
    volumes:
      - ./dist:/usr/share/nginx/html:ro  # 调整源路径
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
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
```

### 重启服务
```bash
# 重启容器
docker-compose restart
```

## 🌐 访问配置

根据您的服务器配置调整：
- **HTTP访问**: http://YOUR_SERVER_IP:PORT
- **HTTPS访问**: https://YOUR_DOMAIN （需配置SSL证书）
- **健康检查**: http://YOUR_SERVER_IP:PORT/health

## 🛑 停止和清理

### 停止服务
```bash
# 停止容器
docker-compose down

# 停止并删除镜像
docker-compose down --rmi all
```

## 🔧 常见项目类型适配

### React项目
```dockerfile
# Dockerfile 修改
COPY build/ /usr/share/nginx/html/
```

### Vue项目
```dockerfile
# Dockerfile 修改
COPY dist/ /usr/share/nginx/html/
```

### Angular项目
```dockerfile
# Dockerfile 修改
COPY dist/your-app-name/ /usr/share/nginx/html/
```

### 纯静态站点
```dockerfile
# Dockerfile 修改
COPY public/ /usr/share/nginx/html/
# 或
COPY . /usr/share/nginx/html/
```

## 🎯 使用说明

1. **复制模板**: 复制此模板到您的项目
2. **调整路径**: 修改构建输出目录路径
3. **配置信息**: 更新IP、端口、容器名等
4. **测试部署**: 在测试环境验证配置
5. **生产部署**: 应用到生产环境

## 💡 最佳实践

- ✅ 使用nginx:alpine镜像（体积小）
- ✅ 启用gzip压缩（提升性能）
- ✅ 配置静态资源缓存
- ✅ 添加健康检查
- ✅ 使用只读卷挂载
- ✅ 设置自动重启策略

## ⚡ 性能优化

### SSL/HTTPS配置
```nginx
server {
    listen 443 ssl;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    # ... 其他配置
}
```

### 多阶段构建（可选）
```dockerfile
# 构建阶段
FROM node:alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 运行阶段
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
``` 