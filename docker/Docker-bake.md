## Docker bake

docker bake 是 Docker 官方在 BuildKit 里提供的一个高级构建命令

主要作用是支持 多目标、多平台的并行构建，并且可以用一个 bake 文件（通常是 docker-bake.hcl 或者 docker-bake.json）定义复杂的构建任务


### `docker bake`命令

执行`docker bake`命令，会自动寻找当前目录的`docker-bake.hcl`文件来自动构建


### 配置文件解释

1：`docker-bake.hcl`仅构建多架构镜像，并且每个架构的镜像分开
```
# 定义一个构建组，组名叫 default
# 这个组包含两个目标：hello1 和 hello2
group "default" {
  targets = ["hello1", "hello2"]
}

# 定义第一个构建目标 hello1
target "hello1" {
  context = "."               # 构建上下文，当前目录（包含 Dockerfile）
  dockerfile = "Dockerfile"  # 指定 Dockerfile 文件路径
  tags = ["hello/alpine:amd64"]  
  platforms = ["linux/amd64"]  # 构建平台为 linux amd64 架构
}

# 定义第二个构建目标 hello2
target "hello2" {
  context = "."               # 构建上下文，当前目录（包含 Dockerfile）
  dockerfile = "Dockerfile"  # 指定 Dockerfile 文件路径
  tags = ["hello/alpine:arm64"]
  platforms = ["linux/arm64"]  # 构建平台为 linux arm64 架构
}
```

2：`docker-bake.hcl`构建完成后自动推送到仓库

需要事先`docker login`登录仓库
```
# 定义构建组 default，只包含一个多平台目标 hello
group "default" {
  targets = ["hello"]
}

# 定义多平台构建目标 hello
target "hello" {
  context = "."               # 构建上下文，当前目录（包含 Dockerfile）
  dockerfile = "Dockerfile"  # 指定 Dockerfile 文件路径

  # 镜像标签，自动推送到远程仓库，记得替换 hello 为你的用户名或仓库名
  tags = ["hello/alpine:latest"]

  # 同时构建 amd64 和 arm64 多个平台
  platforms = ["linux/amd64", "linux/arm64"]

  # 开启构建完成后自动推送镜像，需要事先docker login登录仓库
  push = true
}
```

3：`docker-bake.hcl`同时构建多个任务，并自动推送到仓库

需要事先`docker login`登录仓库
```
group "default" {
  targets = ["php", "nginx"]
}

target "php" {
  context = "."               # 构建上下文
  dockerfile = "Dockerfile.php"  # 第一个 Dockerfile
  tags = ["hello/alpine:latest"]
  platforms = ["linux/amd64", "linux/arm64"]
  push = true # 动推送到仓库
}

target "nginx" {
  context = "."                         # 构建上下文
  dockerfile = "Dockerfile.nginx"       # 第二个 Dockerfile 文件
  tags = ["hello/ubuntu:latest"]
  platforms = ["linux/amd64", "linux/arm64"]
  push = true # 动推送到仓库
}
```

### 指定某个任务文件构建

```
docker bake --file my-bake.hcl
```


### 利用Docker bake批量并发拉取镜像

`Dockerfile`：
```Dockerfile
ARG BASE_IMAGE=scratch
FROM ${BASE_IMAGE}
```


`docker-bake.hcl`：
```
group "default" {
  targets = ["nginx", "alpine", "redis", "mysql", "caddy", "busybox", "python", "node", "golang", "httpd"]
}

target "nginx" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "nginx:latest" }
  tags = ["nginx:latest"]
}

target "alpine" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "alpine:latest" }
  tags = ["alpine:latest"]
}

target "redis" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "redis:latest" }
  tags = ["redis:latest"]
}

target "mysql" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "mysql:latest" }
  tags = ["mysql:latest"]
}

target "caddy" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "caddy:latest" }
  tags = ["caddy:latest"]
}

target "busybox" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "busybox:latest" }
  tags = ["busybox:latest"]
}

target "python" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "python:3.11-slim" }
  tags = ["python:3.11-slim"]
}

target "node" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "node:18-alpine" }
  tags = ["node:18-alpine"]
}

target "golang" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "golang:1.21-alpine" }
  tags = ["golang:1.21-alpine"]
}

target "httpd" {
  context = "."
  dockerfile = "Dockerfile"
  args = { BASE_IMAGE = "httpd:latest" }
  tags = ["httpd:latest"]
}
```
执行拉取命令
```
docker buildx bake
```
