###  最新伊朗版xui

```
mkdir xui && cd xui
```
```
docker run -itd \
    -e XRAY_VMESS_AEAD_FORCED=false \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --network host \
    --name x-ui --restart=unless-stopped \
    alireza7/x-ui:latest
```

```
端口：54321
用户名：admin
密码：admin
```

节点数据在`root/db`目录下

修改用户名和密码
```
docker exec -it x-ui sh
```
```
./x-ui setting -username 用户名 -password 密码
```

重置面板：
```
./x-ui setting -h
```


---

---

---

###  伊朗3x-ui

```
mkdir 3xui && cd 3xui
```
```
docker run -itd \
   -e XRAY_VMESS_AEAD_FORCED=false \
   -v $PWD/db/:/etc/x-ui/ \
   -v $PWD/cert/:/root/cert/ \
   --network=host \
   --restart=unless-stopped \
   --name 3x-ui \
   lovechen/3x-ui:latest
```

```
默认信息
端口：9090
用户名：admin
密码：admin
```

---

---

---

###  FranzKafkaYu/x-ui版本

```
mkdir xui && cd xui
```
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
```
