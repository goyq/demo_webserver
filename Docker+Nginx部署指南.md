# uni-app项目 Docker + Nginx 部署指南

## 📋 项目概述

**技术栈**: uni-app + Docker + Nginx + Docker Compose  
**部署环境**: Linux服务器 (IP: 56.155.89.77)  
**访问端口**: 8080  
**项目路径**: /www/demo_docker

## 🗂️ 服务器文件结构

```
/www/demo_docker/
├── dist/
│   └── h5/
│       ├── index.html
│       └── pages/
│           └── index.vue
├── Dockerfile
├── nginx.conf
└── docker-compose.yml
```

## 🚀 部署步骤

### 1. 切换到项目目录
```bash
cd /www/demo_docker
```

### 2. 创建 Dockerfile
```bash
cat > Dockerfile << 'EOF'
FROM nginx:alpine

# 复制构建好的文件到nginx目录
COPY dist/h5/ /usr/share/nginx/html/

# 复制nginx配置
COPY nginx.conf /etc/nginx/nginx.conf

# 暴露端口
EXPOSE 8080

# 启动nginx
CMD ["nginx", "-g", "daemon off;"]
EOF
```

### 3. 创建 nginx.conf
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
        listen       8080;
        server_name  localhost;
        
        # 主页面配置
        location / {
            root   /usr/share/nginx/html;
            index  index.html;
            try_files $uri $uri/ /index.html;
        }
        
        # 静态资源缓存策略
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
            root   /usr/share/nginx/html;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
        
        # Vue文件处理
        location ~* \.vue$ {
            root   /usr/share/nginx/html;
            add_header Content-Type "text/plain; charset=utf-8";
        }
        
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
  uni-app:
    build: .
    container_name: uni-app-demo
    ports:
      - "8080:8080"
    restart: always
    volumes:
      - ./dist/h5:/usr/share/nginx/html:ro
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8080/health"]
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

## 🌐 访问地址

- **公网访问**: http://56.155.89.77:8080
- **内网访问**: http://172.31.35.124:8080
- **健康检查**: http://56.155.89.77:8080/health

## 🛑 停止和清理

### 停止服务
```bash
# 停止容器
docker-compose down

# 停止并删除镜像
docker-compose down --rmi all
```

### 完全清理
```bash
# 1. 停止容器
docker stop uni-app-demo

# 2. 删除容器
docker rm uni-app-demo

# 3. 删除镜像
docker-compose down --rmi all

# 4. 删除项目文件（谨慎操作）
rm -rf /www/demo_docker
```

## 🔧 故障排查

### 检查容器状态
```bash
docker ps -a
docker-compose logs
```

### 检查端口占用
```bash
netstat -tlnp | grep 8080
```

### 进入容器调试
```bash
docker exec -it uni-app-demo sh
```

## 💡 Docker解耦特性

✅ **环境解耦**: 本地开发环境与生产环境完全隔离  
✅ **依赖解耦**: 无需在服务器安装Node.js等开发环境  
✅ **版本解耦**: 应用使用固定版本的Nginx镜像  
✅ **文件解耦**: 容器运行独立于源文件，删除源文件不影响运行 