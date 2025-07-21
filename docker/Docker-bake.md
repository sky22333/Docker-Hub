## Docker bake

docker bake 是 Docker 官方在 BuildKit 里提供的一个高级构建命令

主要作用是支持 多目标、多平台的并行构建，并且可以用一个 bake 文件（通常是 docker-bake.hcl 或者 docker-bake.json）定义复杂的构建任务


### `docker bake`命令

执行`docker bake`命令，会自动寻找当前目录的`docker-bake.hcl`定义的构建步骤来自动构建


### 配置文件解释

1：`docker-bake.hcl`构建多架构镜像
```
# 定义一个构建组，组名叫 default
# 这个组包含两个目标：hello1 和 hello2
group "default" {
  targets = ["hello1", "hello2"]
}

# 定义第一个构建目标 hello-amd64
target "hello1" {
  context = "."               # 构建上下文，当前目录（包含 Dockerfile）
  dockerfile = "Dockerfile"  # 指定 Dockerfile 文件路径
  tags = ["hello/alpine:amd64"]  
  platforms = ["linux/amd64"]  # 构建平台为 linux amd64 架构
}

# 定义第二个构建目标 hello-arm64
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
  push = true
}

target "nginx" {
  context = "."                         # 构建上下文
  dockerfile = "Dockerfile.nginx"       # 第二个 Dockerfile 文件
  tags = ["hello/ubuntu:latest"]
  platforms = ["linux/amd64", "linux/arm64"]
  push = true
}
```

### 指定某个任务文件构建

```
docker bake --file my-bake.hcl
```
