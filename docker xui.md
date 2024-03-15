###  最新伊朗版xui

```
mkdir xui && cd xui
```
```
docker run -itd \
    --network host \
    -e XRAY_VMESS_AEAD_FORCED=false \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
    alireza7/x-ui:latest
```

```
端口：54321
用户名：admin
密码：admin
```

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
