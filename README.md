# Docker Hub 镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。


### 安装Docker
官方安装脚本：

```
curl -fsSL https://get.docker.com | sh
```


国内安装脚本  [(说明)](https://linuxmirrors.cn/other/)

```
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/DockerInstallation.sh)
```

或者使用阿里云安装源
```
bash <(curl -fsSL https://get.docker.com) --mirror Aliyun
```

<details>
  <summary>手动离线安装Docker</summary>
  
####  下载 Docker:

[官方文件下载地址——下载后上传到root目录](https://download.docker.com/linux/static/stable/x86_64/)

[清华大学下载地址](https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/static/stable/x86_64/)

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
sudo chmod +x /usr/local/bin/dockerd
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
  <summary>手动离线安装Docker-compose</summary>
  

### 国内环境手动安装Docker-compose

[点这里手动下载文件](https://github.com/docker/compose/releases) 上传到服务器的`/usr/local/bin`目录

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


###  注意：
由于是以二进制文件安装的`docker-compose`，所以运行命令有所变化，运行示例
```
docker-compose up -d
```

区别在于中间的`-`，官方安装脚本是以插件形式安装的`docker-compose`，所以中间不需要`-`

---

</details>


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
        "https://docker.1ms.run",
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


#### 如果不方便重启Docker服务，也可以不用设置全局加速地址，拉取镜像时增加加速地址即可，示例：
```
docker pull docker.1panel.live/library/mysql:5.7
```
说明：`library`是一个特殊的命名空间，它代表的是官方镜像。如果是某个用户的镜像就把`library`替换为镜像的用户名。


### Docker Desktop 配置

对于电脑的`Docker Desktop`用户，点击右上角`设置`，找到`Docker Engine`然后修改配置，修改后的示例：
```
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "registry-mirrors": [
    "https://docker.1ms.run",
    "https://hub.rat.dev",
    "https://docker.1panel.live"
  ]
}
```
然后点击右下角的`Apply & restart`保存并重启即可。


### 检查加速是否生效

查看docker系统信息 `docker info`，如果从结果中看到了你配置的加速地址，说明配置成功。

```console
Registry Mirrors:
 [...]
 https://docker.1panel.live
```


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
Environment="HTTP_PROXY=http://127.0.0.1:1080"
Environment="HTTPS_PROXY=http://127.0.0.1:1080"
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
#### 本地代理转发到服务器

使用SSH反向转发把本地的10808端口的流量转发给远程服务器1080端口
```
ssh -R 1080:127.0.0.1:10808 root@服务器地址 -N
```
`-N` 代表仅连接但不打开对话框


---

## 备用方法：打包镜像到本地


1：压缩保存镜像到本地

```
docker save 镜像名 > 镜像名.tar
```

2：手动上传到另一个服务器

3：另一个服务器解压镜像

```
docker load < 镜像名.tar
```
4：查看镜像
```
docker images
```

---

## Docker Hub 镜像测速

拉取镜像时，可使用 `time` 统计所花费的总时间。测速前记得移除本地的镜像。

例如：`time docker pull node:latest`


## 修改客户端并发数加快下载速度

`/etc/docker/daemon.json`

```
{
  "registry-mirrors": [
    "https://docker.1ms.run"
  ],
  "max-concurrent-downloads": 10,
  "max-concurrent-uploads": 10,
  "max-download-attempts": 5,
  "default-ulimits": {
    "nofile": {
      "Hard": 64000,
      "Name": "nofile",
      "Soft": 64000
    }
  }
}
```
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 为Docker启用IPV6

创建或修改`/etc/docker/daemon.json`文件

增加如下配置：
```
{
  "ipv6": true,
  "fixed-cidr-v6": "2001:db8:1::/64"
}
```
重启：`sudo systemctl restart docker`

## 卸载Docker
```
sudo systemctl stop docker
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /etc/docker /var/lib/docker
```

---
## Docker最新稳定加速源列表

提供者 | 镜像加速地址 | 说明 | 加速类型
--- | --- | --- | ---
[1panel](https://1panel.cn/docs/user_manual/containers/setting/) | `https://docker.1panel.live` | 无限制 | Docker Hub
[轩辕镜像](https://docker.xuanyuan.me/) | `https://docker.xuanyuan.me` | 无限制 | Docker Hub
[毫秒镜像](https://docker.1ms.run) | `https://docker.1ms.run` | 有黑名单&可选国内CDN | Docker Hub
[DaoCloud](https://github.com/DaoCloud/public-image-mirror) | `https://docker.m.daocloud.io` |白名单和限流 | Docker Hub
[华为云](https://console.huaweicloud.com/swr/#/swr/dashboard) | `https://***.mirror.swr.myhuaweicloud.com` | 需登录分配 | Docker Hub
[腾讯云](https://cloud.tencent.com/document/product/1207/45596) | `https://mirror.ccs.tencentyun.com` | 仅限腾讯云机器 | Docker Hub
[南京大学](https://doc.nju.edu.cn/books/e1654) | `https://ghcr.nju.edu.cn` | ghcr加速 | ghcr
[南京大学](https://doc.nju.edu.cn/books/e1654) | `https://k8s.nju.edu.cn` | k8s加速 | k8s

## 参考链接

+ https://docs.docker.com/registry/recipes/mirror/
+ https://status.1panel.top/status/docker



##  Docker常用命令:

| 功能    | 命令 | 说明 |
|-------------|-------------------|----------------|
| 编译镜像  | `docker build -t 镜像名 .`      |   先`docker login`登录docker hub        |
| 推送镜像  | `docker push 用户名/镜像名`      |   需先标记镜像 `docker tag 53321f173e 用户名/镜像名`        |
| 查看容器  | `docker ps`      |   `-a`查看包括已停止的容器         |
| 容器资源占用  | `docker stats`      |   查看所有容器资源占用         |
| 容器详细信息  | `docker inspect`      |  挂载看`Mounts`网络看`Networks`       |
| 进入容器内部  | `docker exec -it 容器名 sh`      |   结尾使用`/bash`也行        |
| 创建容器网络  | `docker network create my-network`      |   `my-network`为网络名称        |
| 容器加入网络  | `docker network connect my-network 容器名`      |   替换容器名或ID        |
| 宿主机网络  | `network_mode: host`      |   `docker-compose`使用        |
| 宿主机网络  | `--network host`      |   `docker run`使用        |
| 查看网络  | `docker network inspect my-network`      |   查看`my-network`网络中的容器        |
| 查看变动  | `docker diff 容器名`      |   查看容器里的文件变化        |
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
| 停止并删除  | `docker compose down`  |   停止并删除编排容器        |
| 重新创建容器  | `docker compose up -d --force-recreate`  |   强制删除并重启编排容器   |
| 复制文件  | `docker cp dujiaoka:/app/data.yaml /home`  |   从容器复制到宿主机        |
| 复制文件  | `docker cp /home/data.yaml dujiaoka:/app`  |   从宿主机复制到容器   |

---
[官方仓库](https://hub.docker.com/)

[官方文档](https://docs.docker.com/build/building/packaging/)

---

### `Docker bake`命令介绍

详情见：[Docker-bake](./docker/Docker-bake.md)

它的特点和优势：

- 支持多阶段（multi-target）构建，同时构建多个镜像目标

- 支持多平台交叉编译构建，比如 linux/amd64、linux/arm64 等

- 支持定义依赖关系，构建顺序自动管理

- 配合 BuildKit，速度快且资源利用率高

- 支持使用简单的 JSON 或 HCL 文件配置，清晰明了



### `biuldx`命令介绍

`biuldx`是一个Docker插件，拥有更多高级功能，注意该功能需要 Docker v19.03 以上的版本。


| 功能                         | 命令示例                                                                                                                                      | 说明                                                                                   |
|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
|  跨平台构建  | `docker buildx build --platform linux/amd64,linux/arm64 -t myimage .`                                                                        | 在一个命令中构建适用于多个平台的镜像，支持构建不同架构的镜像（如 `amd64` 和 `arm64`）。   |
|  并行构建 | `docker buildx build --platform linux/amd64,linux/arm64 -t myapp .`                                                                          | 并行构建多个平台版本的镜像，提高构建效率，减少构建时间。                               |
|  多阶段构建  | 在 Dockerfile 中使用 | 通过多个构建阶段优化镜像，通常用于减小镜像体积、分离构建环境与运行环境。               |
|  远程构建   | ```bash<br>docker buildx create --use --name mybuilder --driver docker-container<br>docker buildx build .<br>```                                    | 使用远程构建节点（如云主机、远程 Docker 主机）进行构建，分散构建负载，提高效率。       |
|  导出构建结果| `docker buildx build --output type=docker,name=myimage:latest .`                                                                              | 将构建结果导出为指定格式的文件，如 `.tar` 文件，便于分发、存档或加载到其他环境中。     |
|  使用缓存来加速构建 | `docker buildx build --build-arg BUILDKIT_INLINE_CACHE=1 -t myimage .`                                                                       | 启用 BuildKit 缓存，避免重复构建相同的层，显著提高构建效率。                           |
|  自定义构建输出 | `docker buildx build --output type=local,dest=/path/to/output .`                                                                              | 将构建结果导出到指定路径，支持输出到本地目录、Docker 镜像仓库或其他目标。              |
|  只构建并推送 | `docker buildx build --push -t myimage .`                                                                                                    | 在构建镜像后自动推送到 Docker Registry 或自定义的镜像仓库。                           |
|  构建并缓存镜像 | `docker buildx build --cache-to=type=local,dest=/tmp/cache --cache-from=type=local,src=/tmp/cache -t myimage .`                                 | 使用指定的缓存路径来缓存构建层，避免每次构建都从头开始。                              |
|  限制构建资源 | `docker buildx build --build-arg CPU_LIMIT=2 --build-arg MEMORY_LIMIT=4g -t myimage .`                                                      | 限制构建过程使用的 CPU 和内存资源，确保在资源紧张的环境中运行。                       |
| 构建多镜像 | `docker buildx build --tag myimage:v1 --tag myimage:v2 .`                                                                                  | 使用不同的标签（tag）在一次构建中构建多个镜像版本。                                   |
| Dockerfile 语法检查| `docker buildx build --file Dockerfile.lint .`                                                                                               | 通过语法检查 Dockerfile，确保没有语法错误或不推荐的写法。                              |
|  并行构建多个镜像 | `docker buildx build --tag myapp:latest --tag myapp:v2 --platform linux/amd64,linux/arm64 .`                                                  | 并行构建多个标签和多个平台的镜像，适用于多版本、多架构的镜像构建。                   |


---


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
| 跳转到指定行号          | `:行号`          | 在命令模式中执行      |
| 撤销上一次修改          | `u`              | 命令模式中执行 |
| 搜索关键词  | `/user`      | `n`匹配下一个，`N`匹配上一个          |
| 选择整行          | `v`              | 上下键选择多行 |
| 删除          | `d`              | 删除所选 |
| 光标删除          | `x`              | 删除光标覆盖的内容（向后） |
| 全局替换文本         | `:%s/旧内容/新内容/g`              | 命令模式中执行 |



---
### nano编辑器
| **功能**            | **快捷键**                   | **说明**                                  |
|---------------------|------------------------------|-------------------------------------------|
| **移动光标**        |                              |                                           |
| 行首                | `Ctrl + A`                   | 移动到行首                                |
| 行尾                | `Ctrl + E`                   | 移动到行尾                                |
| 指定行列            | `Ctrl + _`                   | 移动到指定行和列                          |
| **文件操作**        |                              |                                           |
| 保存文件            | `Ctrl + O`                   | 保存文件，按 Enter 确认                   |
| 退出 `nano`         | `Ctrl + X`                   | 退出编辑器，`y`为保存，`n`为不保存                  |
| 另存为              | `Ctrl + O`                   | 输入新文件名保存                          |
| **编辑操作**        |                              |                                           |
| 剪切所选          | `Ctrl + K`                   | 剪切（删除）所选                                |
| 快速选中        | `Ctrl + Shift + ↓`                   | 按一次为一行，按住不动快速多行       |
| 删除当前行          | `Ctrl + K`                   | 删除当前行                                |
| 撤销操作            | `Ctrl + _`                   | 撤销上一步操作                            |
| 重做操作            | `Ctrl + E`                   | 重做上一步操作                            |
| **搜索与替换**      |                              |                                           |
| 搜索                | `Ctrl + W`                   | 搜索指定文本                              |
| 反向搜索            | `Ctrl + W` + `Ctrl + R`      | 进行反向搜索                              |
| 搜索并替换          | `Ctrl + \`                   | 搜索并替换文本                            |
| **行操作**          |                              |                                           |
| 显示行号            | `Ctrl + C`                   | 显示当前光标位置                          |


---


## Stargazers over time
[![Stargazers over time](https://starchart.cc/sky22333/Docker-Hub.svg?variant=adaptive)](https://starchart.cc/sky22333/Docker-Hub)
