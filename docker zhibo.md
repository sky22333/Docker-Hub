## 直播服务器

#### 本项目是自建直播服务器系统，自带前端和后端，可推流直播，拥有在线聊天功能

#### docker运行命令
```
docker run -d -v /data:/app/data -p 8080:8080 -p 1935:1935 owncast/owncast:latest
```

这将绑定data目录，以便您可以访问备份和数据库

前端页面端口：`8080`

后台路径`/admin` 默认用户名`admin` 默认密码`abc123`
