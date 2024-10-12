### Docker部署`L2TP`
```
services:
  softethervpn:
    image: siomiz/softethervpn
    container_name: vpn
    restart: always
    ports:
      - "500:500/udp"         # IPsec/IKE 使用的端口
      - "4500:4500/udp"       # IPsec NAT-T 使用的端口
      - "1701:1701/tcp"       # L2TP 使用的端口
      - "1194:1194/udp"       # OpenVPN 使用的端口（如果启用）
    environment:
      - PSK=yM5XdQXECfR6Xbg7      # 预共享密钥
      - USERNAME=admin            # VPN 用户名
      - PASSWORD=admin7890        # VPN 密码
    cap_add:
      - NET_ADMIN
    volumes:
      - /lib/modules:/lib/modules  # 挂载模块以支持功能
```

> 或者可以直接使用host网络模式：`network_mode: host`

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

