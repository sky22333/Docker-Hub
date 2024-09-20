###  docker部署3xui

```
docker run -itd \
    -e XRAY_VMESS_AEAD_FORCED=false \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --network host \
    --name xui --restart=always \
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

#### 127.0.0.1:62789监听失败是因为服务器没有配置本地环回地址
查看
```
ip addr show lo
```
分配
```
sudo ip addr add 127.0.0.1/8 dev lo
```

---
### 脚本
```
bash <(curl -Ls https://raw.githubusercontent.com/admin8800/x-ui/main/install.sh)
```

---

---

---

###  FranzKafkaYu/x-ui版本

```
docker run -itd --network=host \
    -v $PWD/db/:/etc/x-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name x-ui --restart=always \
    enwaiax/x-ui:alpha-zh
```


```
默认信息
端口： 54321
用户名： admin
密码： admin
```

---

### `docker-compose.yaml`
```
services:
  xui:
    image: dapiaoliang666/x-ui:latest
    container_name: xui
    restart: always
    environment:
      - XRAY_VMESS_AEAD_FORCED=false
    volumes:
      - ./db/:/etc/x-ui/
      - ./cert/:/root/cert/
    ports:
      - "54321:54321"
      - "8080:8080"
      - "80:80"
      - "9988:9988"
      - "19988:19988"
```



---
### Reality域名推荐列表
```
www.cloudflare.com
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
www.tesla.com
swift.com
```


### cf优选域名推荐列表
```
# 稳定大厂域名
unpkg.com
www.visa.com.sg
www.okcupid.com
www.udemy.com
visa.cn
time.cloudflare.com

# 私人维护-分钟级优选
speed.marisalnc.com
cloudflare.182682.xyz
115155.xyz
cdn.2020111.xyz
```



### 反向代理连接内网家宽

- 公网服务器部署`3xui`并创建`2`个节点，例如一个`9988`端口，一个`19988`端口，基本模板-阻止配置-关掉私人IP
- 
- 家宽内网环境部署`3xui`创建一个出站，填入服务端的`19988`端口的节点，然后添加反向代理配置，`互连`选择刚刚添加的`19988`端口的出站节点，`出站`选择`direct`，然后保存并重启，基本模板-阻止配置-关掉私人IP
- 
- 公网服务器的面板添加反向代理，类型选择`门户`，互连选择`19988`端口的节点，入站选择`9988`端口的节点，然后保存并重启
- 
- 然后使用代理软件连接公网服务器的`9988`端口的节点即可，此时这个节点的出站IP就是内网家宽的IP
