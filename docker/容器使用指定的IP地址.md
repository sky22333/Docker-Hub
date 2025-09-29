### 多个公网IP的情况下，给容器使用指定的IP地址

#### 1：创建`macvlan`网络
```
docker network create -d macvlan \
  --subnet=1.2.3.0/24 \
  --gateway=1.2.3.1 \
  -o parent=eth0 macvlan_net
```

- `--subnet`：公网网段
- `--gateway`：系统默认网关
- `parent=eth0`：使用的物理网卡


#### 2：给容器分配公网IP

`ip`就是你要分配的公网IP，这个公网IP必须在宿主机网卡可以查看到

```
docker run -it --rm \
  --network macvlan_net \
  --ip=1.2.3.5 \
  alpine:curl
```

```
docker run -it --rm \
  --network macvlan_net \
  --ip=1.2.3.6 \
  alpine:curl
```

#### 说明

容器网络类似于host，无需映射端口，但是宿主机无法直接访问容器

每个容器都使用指定的公网IP，所以不存在端口冲突问题，在容器内部执行`ip a`也能查看到分配的公网IP
