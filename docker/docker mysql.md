## Docker创建一个mysql 5.7数据库

#### 监听容器IP
```
docker run -d --name mysql -p 172.17.0.1:3306:3306 -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

#### 使用docker host网络模式
```
docker run -d --name mysql --network=host -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```


#### 连接到指定容器网络
```
docker run -d --name mysql --network my_network -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

## 数据库管理

```
docker pull phpmyadmin:latest
```

```
docker run --name my-phpmyadmin -d -p 7890:80 phpmyadmin:latest
```

环境变量：
```
-p 7890:80                 // 映射面板的端口。
-e PMA_HOST=172.17.0.1     // 指定 phpMyAdmin 连接到的数据库主机地址。
-e PMA_PORT=3306           // 指定 MySQL 数据库的端口号。
-e PMA_USER=root           // 指定登录数据库的用户名。
-e PMA_PASSWORD=123456     // 指定登录数据库的密码。
--network my_network       // 指定容器网络
```

#### `docker-compose.yaml`示例连接到指定数据库示例
```
services:
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    ports:
      - "7890:80"
    environment:
      # 文件上传和内存限制设置
      - UPLOAD_LIMIT=64M       # 设置上传文件大小限制为 64MB
      - POST_MAX_SIZE=64M      # 设置 POST 请求大小限制为 64MB
      - MEMORY_LIMIT=128M      # 设置 PHP 内存限制为 128MB
      
      # 数据库连接设置
      - PMA_HOST=mysql                    # MySQL 服务器的主机名，这里假设使用服务名 'mysql'
      - PMA_PORT=3306                     # MySQL 服务器的端口号
      - PMA_USER=acgfakauser              # 连接 MySQL 的用户名
      - PMA_PASSWORD=acgfakapassword      # 连接 MySQL 的密码
      - PMA_DATABASE=acgfakadb            # 默认连接的数据库名称
    
    networks:
      - home_default  # 使用名为 home_default 的网络

networks:
  home_default:
    external: true  # 使用已存在的外部网络
```


---

#### 查看容器网络
```
docker inspect -f '{{.HostConfig.NetworkMode}}' 容器名称或ID
```



---

