### docker部署异次元发卡

安装环境

```
docker run -d -p 3306:3306  -e MYSQL_ROOT_PASSWORD=123456 --name mysql -v /data/mysql/config/my.cnf:/etc/mysql/my.cnf -v /data/mysql/db:/var/lib/mysql mysql:5.7
```


```
docker run -d --name myredis -p 6379:6379 redis --requirepass "123456"
```

部署

```
docker pull weikedata/acgfaka:latest
```

```
docker run -p 8080:80 --name acgfaka -d weikedata/acgfaka:latest
```

使用`ip:8080`访问后台

数据库名称为`mysql`

MySQL地址填docker内部IP`172.17.0.1`

密码都是`123456` 可以在部署命令中自行更改
