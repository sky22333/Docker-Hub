## 数据库管理

```
docker pull phpmyadmin/phpmyadmin
```

```
docker run --name my-phpmyadmin -d -p 8080:80 -e PMA_HOST=172.17.0.1 -e PMA_PORT=3306 -e PMA_USER=root -e PMA_PASSWORD=123456 phpmyadmin/phpmyadmin
```

环境变量：
```
-p 8080:80                 //映射容器的 80 端口到主机的 8080 端口。
-e PMA_HOST=172.17.0.1     //设置 phpMyAdmin 连接到的数据库主机地址。
-e PMA_PORT=3306           //指定 MySQL 数据库的端口号。
-e PMA_USER=root           //设置用于登录数据库的用户名。
-e PMA_PASSWORD=123456     //设置用于登录数据库的密码。
```
