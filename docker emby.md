emby Web界面的端口为 `8096`

使用容器存储数据：

```  
sudo docker run -it --name emby -e TZ=Asia/Shanghai -p 8096:8096 -p 8920:8920 \
            -p 7359:7359/udp -p 1900:1900/udp -d dperson/emby
```

              
              
              
              
使用宿主机存储数据：
```
sudo docker run -it --name emby -e TZ=Asia/Shanghai -p 8096:8096 -p 8920:8920 \
            -p 7359:7359/udp -p 1900:1900/udp \
            -v /path/to/directory:/config \
            -v /path/to/media:/media -d dperson/emby
```





环境变量:
TZ- 配置时区 `TZ=Asia/Shanghai` 为中国上海
USERID- 设置应用程序用户的UID
GROUPID- 为应用程序用户设置GID          








---  
