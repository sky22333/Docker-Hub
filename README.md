# Docker Hub 镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务。

> 原地址 - [Docker Hub 镜像加速](https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6)

## 安装docker
官方安装脚本：

```
curl -fsSL https://get.docker.com | sh
```

国内阿里云镜像

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

Azure 源(中国区 azure)

```
curl -fsSL https://get.docker.com | bash -s docker --mirror AzureChinaCloud
```

<details>
  <summary>安装Docker Compose</summary>
  
  ###  下载 Docker Compose:
运行以下命令来下载 Docker Compose。

国内环境可以[通过这种方法](https://github.com/taotao1058/GitHub-CN/tree/main?tab=readme-ov-file#%E6%96%B9%E6%B3%95%E4%B8%80)下载

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

</details>

##  docker常用命令:

| 功能    | 命令 | 说明 |
|-------------|-------------------|----------------|
| 查看容器  | `docker ps`      |   `-a`查看包括已停止的容器         |
| 容器详细信息  | `docker inspect`      |    `docker inspect 容器名或ID`  挂载点在`Mounts`块       |
| 进入容器内部  | `docker exec -it 容器名 /bin/bash`      |   使用`ls 内部目录`查看所有文件        |
| 停止容器  | `docker stop`      |   `docker stop 容器名或ID`             |
| 启动容器  | `docker start`      | `docker start 容器名或ID`           |
| 重启容器  | `docker restart`      |  `docker restart 容器名或ID`          |
| 删除容器 | `docker rm`       |  `docker rm 容器名或ID`              |
| 查看镜像 | `docker images`   | `docker images 镜像名或ID`            |
| 删除镜像  | `docker rmi -f`   |  `docker rmi -f 镜像名或ID`          |
| 删除所有镜像  | `docker rmi -f $(docker images -aq)`  |   删除所有镜像         |

docker内部安装vim编辑器

```
apt-get update
```
```
apt-get install vim
```

vim输入: `set encoding=utf-8`解决中文乱码问题


## 配置加速地址

> Ubuntu 16.04+、Debian 8+、CentOS 7+

创建或修改 `/etc/docker/daemon.json`：

```
sudo mkdir -p /etc/docker
```
```
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
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

对于 Mac 和 Windows 用户，直接在 Docker Desktop 系统设置中，配置 registry-mirrors 即可。

---
## 检查加速是否生效

命令行执行 `docker info`，如果从结果中看到了如下内容，说明配置成功。

```console
Registry Mirrors:
 [...]
 https://docker.m.daocloud.io
```

## Docker Hub 镜像测速

使用镜像前后，可使用 `time` 统计所花费的总时间。测速前先移除本地的镜像！ 

```console
$ docker rmi node:latest
$ time docker pull node:latest
Pulling repository node
[...]

real   1m14.078s
user   0m0.176s
sys    0m0.120s
```
---
## Docker Hub 镜像加速列表
以下镜像站来源于互联网（感谢热心网友），可能出现宕机、转内网、关停等情况，建议同时配置多个镜像源。

#### 目前可用镜像加速

镜像 | 镜像加速地址 | 说明 | 其它加速
--- | --- | --- | ---
[DaoCloud 镜像站](https://github.com/DaoCloud/public-image-mirror) | `https://docker.m.daocloud.io` | |  Docker Hub、GCR、K8S、GHCR、Quay、NVCR 等
[Docker 镜像代理](https://dockerproxy.com) | `https://dockerproxy.com` | | Docker Hub、GCR、K8S、GHCR
[百度云](https://cloud.baidu.com/doc/CCE/s/Yjxppt74z#%E4%BD%BF%E7%94%A8dockerhub%E5%8A%A0%E9%80%9F%E5%99%A8) | `https://mirror.baidubce.com` | | Docker Hub
[南京大学镜像站](https://doc.nju.edu.cn/books/35f4a) | `https://docker.nju.edu.cn` | | Docker Hub、GCR、GHCR、Quay、NVCR 等
[上海交大镜像站](https://mirrors.sjtug.sjtu.edu.cn/) | `https://docker.mirrors.sjtug.sjtu.edu.cn` | | Docker Hub、GCR 等
[中科院软件所镜像站](https://mirror.iscas.ac.cn/mirror/docker.html) | `https://mirror.iscas.ac.cn` | | Docker Hub

## 参考链接

+ https://docs.docker.com/registry/recipes/mirror/
+ https://github.com/yeasy/docker_practice/blob/master/install/mirror.md
+ https://www.ilanni.com/?p=14534
+ https://moelove.info/2020/09/20/突破-DockerHub-限制全镜像加速服务/
+ https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6
