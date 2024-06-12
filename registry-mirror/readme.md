### 自建Docker镜像加速
需要用外国服务器，支持多种仓库

### 操作

如果你想要启动所有的镜像仓库直接执行 

`docker compose up -d`

但是你想要单独加速某一个仓库就直接进入那个文件夹

`cd dockerhub`

`docker compose up -d`


#### 环境配置教程

此方法会重启Docker服务，当您有容器正在运行时，建议使用下面的方法
```
sudo vim /etc/docker/daemon.json
```
```
{
"registry-mirrors": [
"http://ip:5000"
]
}
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl restart docker
```

查看配置`docker info`

#### 注意：每一个镜像仓库对外的端口都是不一样的，当然你也可以反向代理一下

#### 当你反代并开启HTTPS后，不用设置环境也可以直接使用，用法示例：
```
docker pull example.com/library/mysql:5.7
```
说明：`library`是一个特殊的命名空间，它代表的是官方镜像。如果是某个用户的镜像就把`library`替换为镜像的用户名。

`example.com`为你的域名


### 提示

配置文件在`config.yml`

默认七天之后会清理缓存，也就是你拉取的镜像缓存，您可以自行更改。

如果设置时间过长，那么可能无法跟上镜像的最新版本，如果设置时间过短，那么会给你的服务器流量或者存储造成压力。


### 原理

在您配置镜像加速后，拉取镜像的时候首先会从该代理服务器寻找已经缓存的镜像，如果有，则是直接拉取。如果没有该镜像，那么你的代理服务器会拉取`Docker hub`的镜像并下发给你，同时该镜像会缓存到你的代理服务器。
