### docker快速部署独角数卡

先安装MySQL和Redis：

```
docker run -d -p 3306:3306  -e MYSQL_ROOT_PASSWORD=123456 --name mysql -v /data/mysql/config/my.cnf:/etc/mysql/my.cnf -v /data/mysql/db:/var/lib/mysql mysql:5.7
```

```
docker run -d --name myredis -p 6379:6379 redis --requirepass "123456"
```

然后部署独角数卡

```
docker run -dit --name dujiaoka -p 8111:80 -p 9000:9000 -e WEB_DOCUMENT_ROOT=/app/public jiangjuhong/dujiaoka:latest
```

进入安装页面：

数据库名称为`mysql`


MySQL和Redis地址都填docker内部IP`172.17.0.1`

密码都是`123456`  可以在部署命令中自行更改


用户名和密码都是`admin`，`admin`

后台为`你的IP/admin`

```
docker run -dit --name dujiaoka -p 8111:80 -p 9000:9000 -e ADMIN_HTTPS=true -e WEB_DOCUMENT_ROOT=/app/public jiangjuhong/dujiaoka:latest
```
