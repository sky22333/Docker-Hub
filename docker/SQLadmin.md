## 轻量级数据库管理工具

```
docker run -d --name adminer -p 8080:8080 adminer:latest
```
连接到另一个容器网络可添加变量：`--network mynetwork`

---

查看容器网络
```
docker inspect -f '{{.HostConfig.NetworkMode}}' 容器名称或ID
```


