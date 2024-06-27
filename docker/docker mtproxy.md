**创建白名单镜像：**

```
docker run -d --name mtproxy \
  --restart=always \
  -e domain="tesla.com" \
  -p 8080:80 \
  -p 8443:443 \
  ellermister/mtproxy
```

  

**创建普通镜像：**

```
docker run -d --name mtproxy \
  --restart=always \
  -e domain="tesla.com" \
  -e secret="548593a9c0688f1f7d9d57377897d321" \
  -e ip_white_list="OFF" \
  -p 8080:80 \
  -p 8443:443 \
  ellermister/mtproxy
```

`-e ip_white_list="OFF"`   此行表示关闭白名单

`-e secret="548593a9c0688f4f7d9d57377897d964"`   此行表示指定密钥，格式为：32位十六进制字符。

`-e provider=2 \`      此行代表MTG第三方代理程序， 官方只支持 x86_64

**在日志中查看链接参数配置**：

```
docker logs -f mtproxy
```


连接端口记得修改为你映射后的外部端口，如上文例子中都是 8443

白名单镜像默认所有访客都不被允许连接，只有当访客尝试访问了下面的地址，才会将访客IP加入到白名单中。
IP 和端口取决于你 docker 的配置：
```
http://服务器IP/add.php
```




---
