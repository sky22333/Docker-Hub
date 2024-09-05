### docker部署火狐云浏览器
- 创建项目文件
```
mkdir -p firefox
```
`docker-compose.yaml`配置

```
services:
  firefox:
    image: jlesage/firefox
    container_name: firefox
    ports:
      - "5800:5800"   # noVNC/Web访问端口
      - "5900:5900"   # VNC访问端口
    volumes:
      - ./appdata/firefox:/config:rw          # 配置文件
      - /dev/shm:/dev/shm
    environment:
      - LANG=zh_CN.UTF-8
      - LANGUAGE=zh_CN:zh
      - LC_ALL=zh_CN.UTF-8
      - ENABLE_CJK_FONT=1
      - WEB_AUDIO=1                  # 启用音频
      - TZ=Asia/Shanghai             # 设置时区为上海
      - SECURE_CONNECTION=0          # 启用HTTPS，0为关闭，配合反代使用
      - VNC_PASSWORD=yourpassword    # 设置VNC访问密码
    restart: always
```
- 下载的文件在 `/firefox/appdata/firefox/downloads/`目录下

- 重启
```
docker restart firefox
```
