####  emby Web界面的端口为 `8096`

使用容器存储数据：

```  
sudo docker run -it --name emby -e TZ=Asia/Shanghai -p 8096:8096 -p 8920:8920 \
            -p 7359:7359/udp -p 1900:1900/udp -d emby/embyserver
```

              
              
              
              
使用宿主机存储数据：

确保`/path/to/directory`和`/path/to/media`这两个路径替换为实际的目录路径，这些目录将用于挂载到容器内

```
sudo docker run -it --name emby -e TZ=Asia/Shanghai -p 8096:8096 -p 8920:8920 \
            -p 7359:7359/udp -p 1900:1900/udp \
            -v /path/to/directory:/config \
            -v /path/to/media:/media -d emby/embyserver
```





环境变量:

TZ- 配置时区 `TZ=Asia/Shanghai` 为中国上海

USERID- 设置应用程序用户的UID

GROUPID- 为应用程序用户设置GID        

可选镜像：

`emby/embyserver_arm32v7`

`emby/embyserver_arm64v8`








---  
