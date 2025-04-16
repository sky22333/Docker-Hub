## Docker图形化面板

### 轻量级
```
docker run -d --name dpanel --restart=always \
 -p 8807:8080 -e APP_NAME=dpanel \
 -v /var/run/docker.sock:/var/run/docker.sock -v dpanel:/dpanel \
 dpanel/dpanel:lite
```

> 国内镜像：`registry.cn-hangzhou.aliyuncs.com/dpanel/dpanel:latest`



---

项目地址：https://github.com/donknap/dpanel

