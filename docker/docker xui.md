###  3xui

```
docker run -itd \
    -e XRAY_VMESS_AEAD_FORCED=false \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --network host \
    --name xui --restart=unless-stopped \
    dapiaoliang666/x-ui:latest
```

```
端口：54321
用户名：admin
密码：admin
```

面板数据在`root/db`目录下

查看用户名和密码

```
/app/x-ui setting -show
```

重置面板：
```
/app/x-ui setting -reset
```

`127.0.0.1:62789监听失败可在界内修改入站`

---
### 脚本
```
bash <(curl -Ls https://raw.githubusercontent.com/admin8800/3x-ui/main/install.sh)
```

---

---

---

###  FranzKafkaYu/x-ui版本

```
docker run -itd --network=host \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
    enwaiax/x-ui:alpha-zh
```


```
默认信息
端口： 54321
用户名： admin
密码： admin
```

---


---
### Reality域名推荐列表
```
addons.mozilla.org
s0.awsstatic.com
d1.awsstatic.com
images-na.ssl-images-amazon.com
m.media-amazon.com

player.live-video.net
one-piece.com
lol.secure.dyn.riotcdn.net
www.lovelive-anime.jp
www.swift.com
academy.nvidia.com
www.cisco.com
cdn-dynmedia-1.microsoft.com
update.microsoft
www.tesla.com
www.cloudflare.com

swift.com
```
