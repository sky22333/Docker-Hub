## Home Assistant开源智能家居自动化管理平台

```
docker run -d \
  --name zhineng \
  --network=host \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Asia/Shanghai \
  -v /home/zhineng_config:/config \
  homeassistant/amd64-addon-mosquitto
```

web端口为：`8123`
