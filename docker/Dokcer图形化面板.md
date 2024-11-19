## Docker图形化界面

### 轻量版
```
docker run -it -d --name dpanel --restart=always \
 -p 8807:8080 -e APP_NAME=dpanel \
 -v /var/run/docker.sock:/var/run/docker.sock -v dpanel:/dpanel \
 -e INSTALL_USERNAME=admin -e INSTALL_PASSWORD=admin \
 dpanel/dpanel:lite
```
> 国内镜像：`registry.cn-hangzhou.aliyuncs.com/dpanel/dpanel:latest`<br>默认用户名和密码：`admin`/`admin`



---


