### docker部署异次元发卡

安装数据库

```
docker run -d -p 172.17.0.1:3306:3306  -e MYSQL_ROOT_PASSWORD=123456 --name mysql -v /data/mysql/config/my.cnf:/etc/mysql/my.cnf -v /data/mysql/db:/var/lib/mysql mysql:5.7
```


部署

```
docker pull weikedata/acgfaka:latest
```

```
docker run -p 172.17.0.1:8222:80 --name acgfaka -d weikedata/acgfaka:latest
```

使用域名反代`8222`端口访问和安装(端口只监听docker地址)

数据库名称为`mysql`

MySQL地址填docker内部IP`172.17.0.1`

数据库密码`123456` 可以在部署命令中自行更改
