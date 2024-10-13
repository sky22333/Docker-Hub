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
  openvpn:
    image: kylemanna/openvpn
    container_name: openvpn
    ports:
      - "1194:1194/udp"
    volumes:
      - ./data:/etc/openvpn # 挂载 PKI 数据
    environment:
      - OPENVPN_CONFIG=default
    cap_add:
      - NET_ADMIN
    restart: always
```

初始化 PKI 数据：
```
docker compose exec openvpn ovpn_genconfig -u udp://your-domain-or-ip:1194
docker compose exec openvpn ovpn_initpki
```


将客户端配置导出到宿主机当前目录：
```
docker compose exec openvpn ovpn_getclient client1 > client1.ovpn
```
在客户端上使用 OpenVPN 客户端软件，并加载 client1.ovpn 文件以连接到 VPN。

---

### WireGuard VPN



```
services:
  wg-easy:
    image: ghcr.io/wg-easy/wg-easy
    container_name: wg-easy
    ports:
      - "51820:51820/udp"  # 映射 WireGuard 的 UDP 端口
      - "51821:51821/tcp"  # 映射 Web 界面的 TCP 端口
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
    environment:
      - LANG=en
      - WG_HOST=主机IP或者域名
      - PASSWORD_HASH=面板密码的哈希值
    volumes:
      - ./wireguard:/etc/wireguard
```

[在线生成密码哈希](https://uutool.cn/php-password/)

需要将哈希值的`$`替换为`$$`才是正确的





[官方文档](https://github.com/wg-easy/wg-easy)
