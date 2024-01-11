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



默认信息
端口： `54321`
用户名： `admin`
密码： `admin`

提示:
上述版本为 `FranzKafkaYu/x-ui` 版本（已闭源）
如果希望使用 `vaxilu/x-ui版本` （已停更），仅需要将上述镜像最后一行修改为 `enwaiax/x-ui`