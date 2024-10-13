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
      - "1701:1701/udp"       # L2TP 使用的端口
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
### Docker部署`IKEv2 VPN`
```
services:
  vpn:
    image: mberner/strongswan
    container_name: strongswan
    cap_add:
      - NET_ADMIN
    environment:
      - VPN_DOMAIN=your_public_ip       # 使用你的公网IP
      - VPN_NETWORK=10.10.10.0/24       # VPN的内网网络段
      - VPN_INTERFACE=eth0              # VPN服务器使用的网卡接口
      - VPN_LOCAL_IP=10.10.10.1         # VPN服务器的本地IP
      - VPN_PSK=your_preshared_key      # 设置IPsec预共享密钥
    ports:
      - "500:500/udp"
      - "4500:4500/udp"
    volumes:
      - /lib/modules:/lib/modules:ro
    restart: always
```


---

### Docker部署`OpenVPN`
```
services:
  openvpn-as:
    image: openvpn/openvpn-as
    container_name: openvpn-as
    cap_add:
      - NET_ADMIN
    ports:
      - 943:943
      - 443:443
      - 1194:1194/udp
    volumes:
      - ./:/openvpn
    restart: always
```

- 管理 OpenVPN

面板地址：`https://公网IP:943/admin`

日志
```
docker logs openvpn-as
```

设置密码

```
docker exec -it openvpn-as /bin/bash
```
```
sacli --user "openvpn" --new_pass "ASDqwer789332" SetLocalPassword
```

转到配置 -> `网络设置` -> 将`主机名或 IP 地址`部分更改为您的域名或公网 IP 地址。

---

