###  最新伊朗版xui

```
mkdir xui && cd xui
```
```
docker run -itd \
    -p 8899:8899 \
    -e XRAY_VMESS_AEAD_FORCED=false \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=unless-stopped \
    alireza7/x-ui:latest
```

```
端口：8899
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


---

---

###  国人版本xui

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


提示:
上述版本为 FranzKafkaYu/x-ui 版本（已闭源）
如果希望使用 vaxilu/x-ui版本 （已停更），仅需要将上述镜像最后一行修改为 enwaiax/x-ui
```

---

###  伊朗3xui

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
   ghcr.io/mhsanaei/3x-ui:latest
```

```
默认信息
端口：2053
用户名：admin
密码：admin
```


---
