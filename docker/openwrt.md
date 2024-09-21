### docker部署openwrt系统

- 打开网卡混杂模式，其中`eth0`根据`ifconfig`命令找到自己的本地网卡名称替换
```
sudo ip link set eth0 promisc on
```
- 创建名称为`opnet`的虚拟网卡，并指定网关`gateway`、子网网段`subnet`、虚拟网卡的真实父级网卡`parent`（第一步中的本地网卡名称）
```
docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 opnet
```

- 查看虚拟网卡是否创建成功，成功的话能看到名称为`opnet`的虚拟网卡
```
docker network ls
```

- 启动容器
```
docker run --restart always --name openwrt -d --network opnet --privileged zzsrv/openwrt /sbin/init
```
> 阿里云镜像`registry.cn-hangzhou.aliyuncs.com/zzsrv/openwrt:latest`


- 进入容器根据自己实际情况修改网络配置
```
vim /etc/config/network
```

- 重启容器
```
docker restart openwrt
```
