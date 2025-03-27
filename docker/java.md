### 前后端分离java项目构建示例

`Dockerfile`
```
# 前端构建
FROM node:20 AS frontend-build

WORKDIR /app

COPY ./blogui ./blogui

WORKDIR /app/blogui
RUN npm i
RUN npm run build

# 后端构建
FROM maven:3.8-openjdk-17 AS backend-build

WORKDIR /app

COPY ./blogjava ./blogjava

WORKDIR /app/blogjava
RUN mvn clean package -DskipTests

# 最终运行阶段 - 使用 Caddy 镜像
FROM caddy:2.9-alpine

RUN apk update && \
    apk add --no-cache openjdk17 && \
    rm -rf /var/cache/apk/*

WORKDIR /usr/share/caddy

COPY --from=frontend-build /app/blogui/dist /usr/share/caddy/html
COPY --from=backend-build /app/blogjava/target/blogjava.jar /usr/share/caddy/
COPY ./Caddyfile /etc/caddy/Caddyfile

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk
ENV PATH=$JAVA_HOME/bin:$PATH

CMD ["sh", "-c", "java -jar /usr/share/caddy/blogjava.jar & caddy run --config /etc/caddy/Caddyfile"]
```

`docker-compose.yml`
```
services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: blog123456   # 数据库root密码
      MYSQL_DATABASE: blog              # 数据库名称
      MYSQL_USER: blog                  # 数据库用户名
      MYSQL_PASSWORD: blog123456        # 数据库密码
    volumes:
      - ./sql:/docker-entrypoint-initdb.d  # 挂载 SQL 文件目录，自动执行
    restart: always

  redis:
    image: redis:alpine
    restart: always


  caddy:
    build: .
    ports:
      - "80:80"
      - "443:443"
    environment:
      - DOMAIN=example.com
    restart: always
    depends_on:
      - mysql
      - redis
```

`Caddyfile`vue前端，java后端`8080`端口
```
{$DOMAIN} {
    root * /usr/share/caddy/html
    file_server

    route /api/* {
        reverse_proxy localhost:8080
    }

    try_files {path} /index.html
}
```
