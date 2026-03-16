## 独角Dujiao-Next部署文档docker
### 创建配置文件
```
mkdir -p /opt/dujiao-next/{data/db,data/uploads,data/logs,data/redis,data/postgres}
cd /opt/dujiao-next

# 关键：避免日志/数据库目录权限不足
chmod -R 0777 ./data/logs ./data/db ./data/uploads ./data/redis ./data/postgres
```
### docker-compose.yml配置
```
services:
  redis:
    image: redis:7-alpine
    container_name: dujiaonext-redis
    restart: unless-stopped
    environment:
      REDIS_PASSWORD: redis123
    command: ["redis-server", "--appendonly", "yes", "--requirepass", "redis123"]
    ports:
      - "127.0.0.1:6379:6379"
    volumes:
      - ./data/redis:/data
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "redis123", "ping"]
      interval: 10s
      timeout: 3s
      retries: 10

  api:
    image: dujiaonext/api
    container_name: dujiaonext-api
    restart: unless-stopped
    environment:
      TZ: Asia/Shanghai
      DJ_DEFAULT_ADMIN_USERNAME: admin
      DJ_DEFAULT_ADMIN_PASSWORD: admin123
    ports:
      - "127.0.0.1:8080:8080"
    volumes:
      - ./config.yml:/app/config.yml:ro
      - ./data/db:/app/db
      - ./data/uploads:/app/uploads
      - ./data/logs:/app/logs
    depends_on:
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://127.0.0.1:8080/health"]
      interval: 10s
      timeout: 3s
      retries: 10

  user:
    image: dujiaonext/user
    container_name: dujiaonext-user
    restart: unless-stopped
    environment:
      TZ: Asia/Shanghai
    ports:
      - "127.0.0.1:8081:80"
    depends_on:
      api:
        condition: service_healthy

  admin:
    image: dujiaonext/admin
    container_name: dujiaonext-admin
    restart: unless-stopped
    environment:
      TZ: Asia/Shanghai
    ports:
      - "127.0.0.1:8082:80"
    depends_on:
      api:
        condition: service_healthy
```
### 当前目录创建config.yml配置
```
database:
  driver: sqlite
  dsn: /app/db/dujiao.db

# Redis 配置
redis:
  enabled: true
  host: redis
  port: 6379
  password: "redis123"
  db: 0
  prefix: "dj"

# 队列配置
queue:
  enabled: true
  host: redis
  port: 6379
  password: "redis123"
  db: 1
  concurrency: 5
  queues:
    default: 5
    critical: 2

# JWT 配置
jwt:
  secret: "TestJWTSecret"
user_jwt:
  secret: "TestUserJWTSecret"
```

按需使用PostgreSQL数据库

官方文档：https://dujiao-next.com/deploy/docker-compose


## caddy反代配置
```
# 前台
shop.example.com {
    encode gzip zstd
    handle /api/* {
        reverse_proxy 127.0.0.1:8080
    }
    handle /uploads/* {
        reverse_proxy 127.0.0.1:8080
    }
    handle {
        reverse_proxy 127.0.0.1:8081
    }
}

# 管理后台
admin.example.com {
    encode gzip zstd
    handle /api/* {
        reverse_proxy 127.0.0.1:8080
    }
    handle /uploads/* {
        reverse_proxy 127.0.0.1:8080
    }
    handle {
        reverse_proxy 127.0.0.1:8082
    }
}
```
