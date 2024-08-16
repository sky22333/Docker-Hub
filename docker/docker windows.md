### Docker安装windows系统


### 加速选项
要验证您的系统是否支持 KVM，请运行以下命令：
```
sudo apt install cpu-checker
sudo kvm-ok
```


### 启动
```
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "win11"
      LANGUAGE: "Chinese"
      CPU_CORES: "4"
      RAM_SIZE: "8G"
      DISK_SIZE: "64G"
      USERNAME: "用户名"
      PASSWORD: "密码"
    devices:
      - /dev/kvm
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    stop_grace_period: 2m
```

-   启动容器并使用您的 Web 浏览器连接到端口 `8006` 可查看进度

环境变量：
`VERSION: "https://example.com/win.iso"` 指定镜像链接

映射镜像文件：
```
volumes:
  - /home/user/example.iso:/custom.iso
``` 



项目地址：https://github.com/dockur/windows

MAC系统：https://github.com/dockur/macos
