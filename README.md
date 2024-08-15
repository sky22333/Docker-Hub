# Docker Hub 镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。

[自建Docker镜像加速](https://github.com/sky22333/Docker-Hub/tree/main/registry-mirror) 支持多种仓库

### 安装Docker（如果安装困难可以选择手动安装）
官方安装脚本：

```
curl -fsSL https://get.docker.com | sh
```

国内安装脚本  [(说明)](https://linuxmirrors.cn/other/)

```
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/DockerInstallation.sh)
```

<details>
  <summary>手动安装Docker</summary>
  
####  下载 Docker:

[文件下载地址—需要代理——下载后上传到root目录](https://download.docker.com/linux/static/stable/x86_64/)

```
tar xzvf docker-26.1.3.tgz     # 替换版本号
sudo mv docker/* /usr/local/bin/
```
#### 创建 Docker 服务文件
```
sudo vim /etc/systemd/system/docker.service
```
添加以下内容
```
[Unit]
Description=Docker Application Container Engine
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/local/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
Restart=always
RestartSec=2
StartLimitBurst=3
StartLimitInterval=60s
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
Delegate=yes
KillMode=process

[Install]
WantedBy=multi-user.target
```

#### 启动并启用 Docker 服务
```
sudo chmod +x /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo systemctl start docker
sudo systemctl enable docker.service
```
#### 查看版本
```
docker -v
```




</details>


<details>
  <summary>安装Docker Compose</summary>
  
  ###  下载 Docker Compose:



运行以下命令来下载 Docker Compose：

```
curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
添加可执行权限:
```
chmod +x /usr/local/bin/docker-compose
```
验证安装:
```
docker-compose --version
```

---
### 国内环境手动安装Docker Compose

[手动下载文件](https://github.com/docker/compose/releases) 上传到服务器的`/usr/local/bin`目录

重命名为docker-compose
```
sudo cp docker-compose-linux-x86_64 /usr/local/bin/docker-compose
```
增加执行权限
```
chmod +x /usr/local/bin/docker-compose
```
验证安装
```
docker-compose --version
```

---

</details>

##  Docker常用命令:

| 功能    | 命令 | 说明 |
|-------------|-------------------|----------------|
| 编译镜像  | `docker build -t 镜像名 .`      |   先`docker login`登录docker hub        |
| 推送镜像  | `docker push 用户名/镜像名`      |   需先标记镜像 `docker tag 53321f173e 用户名/镜像名`        |
| 查看容器  | `docker ps`      |   `-a`查看包括已停止的容器         |
| 容器资源占用  | `docker stats`      |   查看所有容器资源占用         |
| 容器详细信息  | `docker inspect`      |  挂载看`Mounts`网络看`Networks`       |
| 进入容器内部  | `docker exec -it 容器名 /bin/sh`      |   结尾使用`/bash`也行        |
| 创建容器网络  | `docker network create my-network`      |   `my-network`为网络名称        |
| 容器加入网络  | `docker network connect my-network 容器名`      |   替换容器名或ID        |
| 查看网络  | `docker network inspect my-network`      |   查看`my-network`网络中的容器        |
| 停止容器  | `docker stop`      |   `docker stop 容器名或ID`             |
| 启动容器  | `docker start`      | `docker start 容器名或ID`           |
| 重启容器  | `docker restart`      |  `docker restart 容器名或ID`          |
| 删除容器 | `docker rm`       |  `docker rm 容器名或ID`              |
| 查看镜像 | `docker images`   | `docker images 镜像名或ID`            |
| 删除镜像  | `docker rmi -f`   |  `docker rmi -f 镜像名或ID`          |
| 清除资源  | `docker system prune`   |  清除所有未使用资源`容器 网络 镜像 缓存`    |
| 删除所有镜像  | `docker rmi -f $(docker images -aq)`  |   删除所有镜像         |
| 删除所有容器  | `docker container prune -f`  |   删除所有已停止容器         |
| 停止所有容器  | `docker stop $(docker ps -aq)`  |   停止所有容器         |

---
[官方仓库](https://hub.docker.com/)

[官方文档](https://docs.docker.com/build/building/packaging/)

---

### vim编辑器

安装：`apt update` `apt install curl wget git zip vim -y`或者`apk add vim`

常用命令：

| 功能    | 命令 | 说明 |
|-------------|-------------------|----------------|
| 退出并保存  | `:wq`      |   命令模式中执行         |
| 只退出不保存  | `:q!`      |    命令模式中执行      |
| 清空所有内容  | `:%d`      |   命令模式中执行        |
| 粘贴时保持代码格式  | `:set paste`      |   执行后按`i`进入编辑模式             |
| 进入编辑模式  | `i`      | 命令模式中执行          |
| 退出编辑模式  | `esc`      |  编辑模式中执行         |
| 更改编码适配中文 | `:set encoding=utf-8`       |  执行后按`i`进入编辑模式              |


---

## 配置加速地址

> Ubuntu 16.04+、Debian 8+、CentOS 7+

创建或修改 `/etc/docker/daemon.json`：

```
sudo mkdir -p /etc/docker
```
```
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://hub.rat.dev",
        "https://docker.1panel.live"
    ]
}
EOF
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl restart docker
```


#### 如果您当前有正在运行的容器不方便重启Docker服务，则不用设置环境也可以直接使用，用法示例：
```
docker pull docker.1panel.live/library/mysql:5.7
```
说明：`library`是一个特殊的命名空间，它代表的是官方镜像。如果是某个用户的镜像就把`library`替换为镜像的用户名。


### 检查加速是否生效

查看docker系统信息 `docker info`，如果从结果中看到了如下内容，说明配置成功。

```console
Registry Mirrors:
 [...]
 https://docker.1panel.live
```

对于 Mac 和 Windows 用户，直接在 Docker Desktop 系统设置中，配置 registry-mirrors 即可。

---
## 使用代理拉取镜像

#### 创建配置文件
```
sudo mkdir -p /etc/systemd/system/docker.service.d
```
```
sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf
```
#### 在文件中添加代理
```
[Service]
Environment="HTTP_PROXY=socks5://user:pass@127.0.0.1:1080"
Environment="HTTPS_PROXY=socks5://user:pass@127.0.0.1:1080"
```
#### 重启Docker
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
#### 查看环境变量
```
sudo systemctl show --property=Environment docker
```
---
## 备用方法：直接传送镜像
国外服务器拉取镜像后打包压缩到本地，然后传输到国内服务器，`mysql`为例
#### A服务器压缩保存Docker镜像
```
docker save mysql > mysql.tar
```
#### 传送到B服务器
```
scp mysql.tar root@192.168.12.23:/home
```
然后输入B服务器root密码

#### B服务器加载Docker镜像

```
cd /home
```

```
docker load < mysql.tar
```
查看镜像
```
docker images
```
---

## Docker Hub 镜像测速

拉取镜像时，可使用 `time` 统计所花费的总时间。测速前记得移除本地的镜像。

例如：`time docker pull node:latest`

## 卸载Docker
```
sudo systemctl stop docker
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /etc/docker /var/lib/docker
```

---
## Docker Hub 镜像加速列表
以下镜像站来源于互联网（感谢热心网友），可能出现宕机、转内网、关停等情况，建议同时配置多个镜像源。

#### 目前可用镜像加速

镜像 | 镜像加速地址 | 说明 | 其它加速
--- | --- | --- | ---
[网友提供](https://hub.rat.dev/) | `https://hub.rat.dev` | | Docker Hub
[1panel](https://1panel.cn/docs/user_manual/containers/setting/) | `https://docker.1panel.live` | | Docker Hub
[阿里云](https://cr.console.aliyun.com/) | `https://<your_code>.mirror.aliyuncs.com` | 需登录分配 | Docker Hub 镜像不是最新的

## 参考链接

+ https://docs.docker.com/registry/recipes/mirror/
