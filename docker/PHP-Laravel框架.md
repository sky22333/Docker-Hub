### `PHP-Laravel`框架镜像打包模板

- 配置基于`BeikeShop`商城项目
- 源码基于[官网下载页](https://beikeshop.cn/download)的`v1.5.6`版本

`Dockerfile`配置
```
FROM webdevops/php-nginx:8.1-alpine

# 设置站点运行目录
ENV WEB_DOCUMENT_ROOT /app/public

# 设置伪静态规则
ENV NGINX_REWRITE_RULES "location / { try_files \$uri \$uri/ /index.php?\$query_string; }"

COPY . /app

RUN chmod -R 777 /app

EXPOSE 80

CMD ["supervisord"]
```

`docker-compose.yml`配置
```
services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    environment:
      MYSQL_DATABASE: dulizhan
      MYSQL_USER: dulizhan
      MYSQL_PASSWORD: dulizhan7890
      MYSQL_ROOT_PASSWORD: dulizhan7890
    volumes:
      - ./mysql:/var/lib/mysql
    restart: always

  web:
    build: .
    ports:
      - "8080:80"
    restart: always
    depends_on:
      - mysql
```
