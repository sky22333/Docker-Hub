### adguardhome自建DNS

- 需要占用系统的53端口

```
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    restart: always
    network_mode: "host"
    volumes:
      - ./work:/opt/adguardhome/work
      - ./conf:/opt/adguardhome/conf
    environment:
      - TZ=Asia/Shanghai
```
