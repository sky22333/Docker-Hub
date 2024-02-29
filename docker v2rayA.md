### v2rayA的liunx系统代理工具

```
docker run -d \
  --restart=always \
  --privileged \
  --network=host \
  --name v2raya \
  -e V2RAYA_LOG_FILE=/tmp/v2raya.log \
  -e V2RAYA_V2RAY_BIN=/usr/local/bin/v2ray \
  -e V2RAYA_NFTABLES_SUPPORT=off \
  -e IPTABLES_MODE=legacy \
  -v /lib/modules:/lib/modules:ro \
  -v /etc/resolv.conf:/etc/resolv.conf \
  -v /etc/v2raya:/etc/v2raya \
  mzz2017/v2raya
```


#### 环境变量
```
export http_proxy=http://127.0.0.1:20171
export https_proxy=http://127.0.0.1:20171
```
默认情况下 v2rayA 会通过核心开放 20170(socks5), 20171(http)


#### 透明代理配置

![alt](/png/touming.jpg)
