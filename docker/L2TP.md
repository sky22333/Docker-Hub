### Docker部署`L2TP`
```
services:
  softethervpn:
    image: siomiz/softethervpn
    container_name: vpn
    restart: always
    ports:
      - "500:500/udp"
      - "4500:4500/udp"
      - "1701:1701/tcp"
      - "1194:1194/udp"
    environment:
      - PSK=yM5XdQXECfR6Xbg7      # 预共享密钥
      - USERNAME=admin            # VPN 用户名
      - PASSWORD=admin7890        # VPN 密码
    cap_add:
      - NET_ADMIN
    volumes:
      - /lib/modules:/lib/modules  # 挂载模块以支持功能
```

---

### Docker部署`OpenVPN`
```
services:
  openvpn:
    image: kylemanna/openvpn
    container_name: openvpn
    restart: always
    ports:
      - "1194:1194/udp"                     # 可以替换为tcp
    environment:
      OVPN_SERVER: "udp://123.123.123.123"    # 替换为您的服务器地址
      OVPN_NETWORK: "10.8.0.0/24"              # VPN 网络地址
      OVPN_DNS: "8.8.8.8"
      OVPN_PWD: "your_password"             # 用户密码（如果需要）
    volumes:
      - ./openvpn:/etc/openvpn
```

- 初始化 OpenVPN
```
# 生成配置
docker run -v $(pwd)/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://123.123.123.123

# 初始化 PKI
docker run -v $(pwd)/openvpn:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki
```

---

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
