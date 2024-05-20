## Docker-compose 安装极光转发面板

### 创建文件：
```
mkdir -p ~/aurora && cd ~/aurora && touch docker-compose.yml
```

### 写入配置：

```
version: '3.8'
services:
  worker:
    image: leishi1313/aurora-admin-backend:latest
    restart: always
    command: bash worker.sh
    environment:
      TZ: 'Asia/Shanghai'
      ENABLE_SENTRY: 'yes'
      DATABASE_URL: 'postgresql://aurora:AuroraAdminPanel321@postgres:5432/aurora'
      TRAFFIC_INTERVAL_SECONDS: 600
      DDNS_INTERVAL_SECONDS: 120
    volumes:
      - $HOME/.ssh/id_rsa:/app/ansible/env/ssh_key
      - app-data:/app/ansible/priv_data_dirs
    depends_on:
      - redis
    networks:
      - aurora
      - worker

  backend:
    image: leishi1313/aurora-admin-backend:latest
    restart: always
    command: bash -c "while !</dev/tcp/postgres/5432; do sleep 1; done; alembic upgrade heads && python app/main.py"
    environment:
      TZ: 'Asia/Shanghai'
      PYTHONPATH: .
      DATABASE_URL: 'postgresql://aurora:AuroraAdminPanel321@postgres:5432/aurora'
      ENABLE_SENTRY: 'yes'
      SECREY_KEY: 'AuroraAdminPanel321'
    volumes:
      - app-data:/app/ansible/priv_data_dirs
    depends_on:
      - postgres
      - redis
    networks:
      - aurora

  nginx:
    image: leishi1313/aurora-admin-frontend:latest
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
    ports:
      - 8000:80
    depends_on:
      - backend
    networks:
      - aurora

  redis:
    image: redis
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
    networks:
      - aurora

  postgres:
    image: postgres:13-alpine
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
      POSTGRES_USER: aurora
      POSTGRES_PASSWORD: AuroraAdminPanel321
      POSTGRES_DB: aurora
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - aurora


volumes:
  db-data:
  app-data:

networks:
  worker:
    enable_ipv6: false
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "true"
    ipam:
      driver: default
      config:
        - subnet: fd00:ea23:9c80:4a54:e242:5f97::/96
          gateway: fd00:ea23:9c80:4a54:e242:5f97::1
  aurora:
    driver: bridge
```

面板端口为：`8000`


### （可选）开启ipv6支持：
```
1：找到 enable_ipv6: false 该行，将 false 改为 true

2：输入以下命令（注意，重启系统会导致规则被重置，需重新添加）

ip6tables -t nat -A POSTROUTING -s fd00:ea23:9c80:4a54:e242:5f97::/96 -j MASQUERADE
```

### 启动面板：
```
docker compose up -d
```

### 创建管理员用户（密码必须设置8位以上，否则无法登陆）：
```
docker compose exec backend python app/initial_data.py
```


