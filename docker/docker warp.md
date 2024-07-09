### docker版warp

[官方仓库](https://hub.docker.com/r/monius/docker-warp-socks)

### 1：普通部署

```
docker run --privileged --restart=always -itd \
    --name warp_socks \
    --cap-add NET_ADMIN \
    --cap-add SYS_MODULE \
    --sysctl net.ipv6.conf.all.disable_ipv6=0 \
    --sysctl net.ipv4.conf.all.src_valid_mark=1 \
    -v /lib/modules:/lib/modules \
    -p 9091:9091 \
    monius/docker-warp-socks
```

上述命令将创建一个后台服务，使整个容器网络可以加入双栈 cloudflare 网络池，而无需断开与主机的连接。

此时你的9091端口的所有流量将通过WARP出站

查看你的warp的ip：`curl -x "socks5h://127.0.0.1:9091" -fsSL "https://ifconfig.co"`


### 2：WARP Plus 账户
```
docker run --privileged --restart=always -itd \
    --name warp_socks_plus \
    -e WGCF_LICENSE_KEY=你的plus密钥 \
    --cap-add NET_ADMIN \
    --cap-add SYS_MODULE \
    --sysctl net.ipv6.conf.all.disable_ipv6=0 \
    --sysctl net.ipv4.conf.all.src_valid_mark=1 \
    -v /lib/modules:/lib/modules \
    -p 9091:9091 \
    monius/docker-warp-socks
```
运行`curl -x "socks5h://127.0.0.1:9091" -fsSL "https://www.cloudflare.com/cdn-cgi/trace"` 看到`plus`则表示WARP Plus密钥应用成功。

### 3：sk5增加密码

```
docker run --privileged --restart=always -itd \
    --name warp_socks_passwd \
    -e SOCK_USER=用户名 \
    -e SOCK_PWD=密码 \
    --cap-add NET_ADMIN \
    --cap-add SYS_MODULE \
    --sysctl net.ipv6.conf.all.disable_ipv6=0 \
    --sysctl net.ipv4.conf.all.src_valid_mark=1 \
    -v /lib/modules:/lib/modules \
    -p 9091:9091 \
    monius/docker-warp-socks
```
验证是否成功`curl -U "用户名:密码" -x "socks5h://127.0.0.1:9091" -fsSL "https://www.cloudflare.com/cdn-cgi/trace"`


### 4：自定义wireguard配置
```
docker run --privileged --restart=always -itd \
    --name warp_socks \
    --cap-add NET_ADMIN \
    --cap-add SYS_MODULE \
    --sysctl net.ipv6.conf.all.disable_ipv6=0 \
    --sysctl net.ipv4.conf.all.src_valid_mark=1 \
    -p 127.0.0.1:9091:9091 \
    -v /lib/modules:/lib/modules \
    -v ~/wireguard/:/opt/wireguard/:ro \
    monius/docker-warp-socks
```
创建`wireguard`目录，并创建`danted.conf`文件填入你的`wireguard`配置
