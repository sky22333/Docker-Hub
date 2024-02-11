### docker运行xray

创建配置文件`/etc/xray/config.json`

运行：

```
docker run -d -p 9000:9000 --name xray --restart=always -v /etc/xray:/etc/xray teddysun/xray
```

这里的端口要与配置文件里的入站端口相同

---
---
