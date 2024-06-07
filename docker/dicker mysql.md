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
