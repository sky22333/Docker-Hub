### 中转机
```
services:
  gost:
    image: ginuerzh/gost
    container_name: gost
    restart: always
    network_mode: host
    volumes:
      - ./cert:/cert  # 存放证书的路径
    command: >
      -L=:500
      -F=udp://落地机:7777
      -L=:4500
      -F=udp://落地机:7777
      -L=:1701
      -F=tcp://落地机:7777
      -L=:1194
      -F=udp://落地机:7777
      -L=:8443
      -F=wss://落地机:8443
      -cert=/cert/server.crt
      -key=/cert/server.key

```

---

### 落地机
```
services:
  gost:
    image: ginuerzh/gost
    container_name: gost
    restart: always
    network_mode: host
    volumes:
      - ./cert:/cert  # 存放证书的路径
    command: >
      -L=:7777
      -F=udp://127.0.0.1:500
      -L=:7777
      -F=udp://127.0.0.1:4500
      -L=:7777
      -F=tcp://127.0.0.1:1701
      -L=:7777
      -F=udp://127.0.0.1:1194
      -L=:8443
      -F=wss://127.0.0.1:8443
      -cert=/cert/server.crt
      -key=/cert/server.key
```

---

### 注释
`-L`选项为监听本地`入口`端口，`-F`选项为监听`出口`端口


---

```
生成私钥
openssl genrsa -out server.key 2048
```
```
生成自签证书
openssl req -new -x509 -key server.key -out server.crt -days 365
```

在运行此命令时，系统会提示你输入一些信息（如国家、组织、通用名称等）。通用名称（Common Name）通常设置为你要使用的域名或 IP 地址。

---

```
将证书复制到受信任证书目录：
sudo cp server.crt /usr/local/share/ca-certificates/
```
```
更新证书存储：
sudo update-ca-certificates
```
