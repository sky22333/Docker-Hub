## Docker安装WireGuard面板


```
docker run -d \
  --name=wg-easy \
  -e LANG=en \
  -e WG_HOST=YOUR_SERVER_IP \
  -e PASSWORD=YOUR_ADMIN_PASSWORD \
  -v ~/.wg-easy:/etc/wireguard \
  -p 51820:51820/udp \
  -p 51821:51821/tcp \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_MODULE \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --sysctl="net.ipv4.ip_forward=1" \
  --restart unless-stopped \
  ghcr.io/wg-easy/wg-easy
```

#### 说明
[Docker hub的镜像](https://hub.docker.com/r/weejewel/wg-easy/tags)


> 💡 替换`YOUR_SERVER_IP`为您的主机IP地址。
>
> 💡 替换`YOUR_ADMIN_PASSWORD`为登录 Web UI 的密码。

Web UI 默认端口`http://0.0.0.0:51821`

> 💡 您的配置文件将保存在`~/.wg-easy`

WireGuard 也可以通过 Docker Compose 启动 - 参考[docker-compose.yml配置](https://github.com/wg-easy/wg-easy/blob/master/docker-compose.yml)



#### 环境变量

| 变量 | 默认 | 例子 | 说明 |
| - | - | - | - |
| `PORT` | `51821` | `6789` | Web UI 的 TCP 端口。 |
| `WEBUI_HOST` | `0.0.0.0` | `localhost` | Web UI 绑定到的 IP 地址。 |
| `PASSWORD` | - | `foobar123` | 设置后，登录 Web UI 时需要密码。 |
| `WG_HOST` | - | `vpn.myserver.com` | VPN 服务器的公共主机名。 |
| `WG_DEVICE` | `eth0` | `ens6f0` | Wireguard 流量应通过以太网设备转发。 |
| `WG_PORT` | `51820` | `12345` | VPN 服务器的公共 UDP 端口。WireGuard 将始终侦听 Docker 容器内的 51820。 |
| `WG_MTU` | `null` | `1420` | 客户端将使用的 MTU。服务器使用默认 WG MTU。 |
| `WG_PERSISTENT_KEEPALIVE` | `0` | `25` | 保持“连接”打开的值（以秒为单位）。如果该值为 0，则连接将不会保持活动状态。 |
| `WG_DEFAULT_ADDRESS` | `10.8.0.x` | `10.6.0.x` | 客户端 IP 地址范围。 |
| `WG_DEFAULT_DNS` | `1.1.1.1` | `8.8.8.8, 8.8.4.4` | 客户端将使用 DNS 服务器。如果设置为空白值，客户端将不会使用任何 DNS。 |
| `WG_ALLOWED_IPS` | `0.0.0.0/0, ::/0` | `192.168.15.0/24, 10.0.1.0/24` | 客户端将使用的允许 IP。 |
| `WG_PRE_UP` | `...` | - | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L19) for the default value. |
| `WG_POST_UP` | `...` | `iptables ...` | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L20) for the default value. |
| `WG_PRE_DOWN` | `...` | - | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L27) for the default value. |
| `WG_POST_DOWN` | `...` | `iptables ...` | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L28) for the default value. |
| `LANG` | `en` | `de` | Web UI 语言（支持：en、ua、ru、tr、no、pl、fr、de、ca、es、ko、vi、nl、is、pt、chs、cht、it、th、hi）|
| `UI_TRAFFIC_STATS` | `false` | `true` | 在 Web UI 中启用详细的 RX/TX 客户端统计信息 |

> 如果您进行更改`WG_PORT`，请确保同时更改暴露的端口。
> 更多配置请参考 [官方教程](https://github.com/wg-easy/wg-easy)
