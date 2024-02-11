## 使用docker部署hysteria2代理

#### 创建文件

```
mkdir /etc/hysteria
```

#### 创建配置文件

```
nano /etc/hysteria/config.json
```


```
{
  "listen": ":37210",
  "cert": "/etc/hysteria/ssl/my.crt",
  "key": "/etc/hysteria/ssl/my.key",
  "obfs": "fuck me till the daylight",
  "up_mbps": 100,
  "down_mbps": 100
}
```


#### 运行容器

```
docker run -dt --network=host --restart=always --name hysteria -v
/etc/hysteria/:/etc/hysteria/ -v
/etc/hysteria/config.json:/etc/hysteria/config.json tobyxdd/hysteria -config
/etc/hysteria/config.json server
```

---

### 客户端config.json文件

```
{
  "server": "域名:36712",
  "obfs": "fuck me till the daylight",
  "up_mbps": 30,
  "down_mbps": 100,
  "socks5": {
    "listen": "127.0.0.1:1080"
  },
  "http": {
    "listen": "127.0.0.1:8080"
  }
}
```


---
---
