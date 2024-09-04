### docker部署firefox云浏览器
- 创建项目文件
```
mkdir -p firefox
```
`docker-compose.yaml`配置

```
services:
  firefox:
    image: linuxserver/firefox:latest
    container_name: firefox
    environment:
      - PUID=1000          # 用户ID，根据需要调整
      - PGID=1000          # 组ID，根据需要调整
      - TZ=Asia/Shanghai   # 时区设置，修改为你所在的时区
    volumes:
      - ./config:/config   # 配置文件持久化路径
      - ./downloads:/downloads  # 下载文件保存路径
    ports:
      - 3000:3000           # 映射端口
    shm_size: "2gb"         # 共享内存大小，防止崩溃
    restart: always
```
