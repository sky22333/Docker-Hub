### Docker安装windows系统


#### 配置中会默认开启KVM优化性能
要验证您的系统是否支持 KVM，请运行以下命令：
```
sudo apt install cpu-checker
sudo kvm-ok
```
> 如果没有KVM导致启动失败，说明你的服务器不允许虚拟化嵌套，可以在环境变量中添加`KVM: "N"`来取消KVM加速，并且注释或删除`devices部分`，不过这样会导致性能下降，使用会非常卡顿。

### Docker-compose配置
```
services:
  windows:
    image: dockurr/windows
    container_name: win
    environment:
      VERSION: "11"
      LANGUAGE: "Chinese"
      CPU_CORES: "4"
      RAM_SIZE: "8G"
      DISK_SIZE: "64G"
      USERNAME: "用户名"
      PASSWORD: "密码"
    devices:
      - /dev/kvm
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    stop_grace_period: 2m
```
启动
```
docker compose up -d
```

-   启动容器并使用您的 Web 浏览器连接到端口 `8006` 可查看进度
-   安装完成后通过`3389`端口远程连接

指定镜像链接，环境变量：
`VERSION: "https://example.com/win.iso"`

映射镜像文件：
```
volumes:
  - /home/example.iso:/custom.iso
``` 

文件映射：
```
volumes:
  -  /home:/data
```
打开`文件资源管理器`，点击`网络`部分，找到名为`host.lan`的计算机。双击它，有个名为`Data`的文件夹，映射的文件在此目录。

---

项目地址：https://github.com/dockur/windows

MAC系统：https://github.com/dockur/macos





  | **变量值** | **版本**              | **系统大小** |
  |---|---|---|
  | `win11`   | Windows 11 专业版           | 6.4 GB   |
  | `win11e`  | Windows 11 企业版    | 5.8 GB   |
  | `win10`   | Windows 10 专业版           | 5.7 GB   |
  | `ltsc10`  | Windows 10 LTSC          | 4.6 GB   |
  | `win10e`  | Windows 10 企业版    | 5.2 GB   |
  ||||  
  | `win8`    | Windows 8.1 专业版          | 4.0 GB   |
  | `win8e`   | Windows 8.1 企业版   | 3.7 GB   |
  | `win7`    | Windows 7 企业版     | 3.0 GB   |
  | `vista`   | Windows Vista 企业版 | 3.0 GB   |
  | `winxp`   | Windows XP 专业版  | 0.6 GB   |
  ||||
  | `2022`    | Windows Server 2022      | 4.7 GB   |
  | `2019`    | Windows Server 2019      | 5.3 GB   |
  | `2016`    | Windows Server 2016      | 6.5 GB   |
  | `2012`    | Windows Server 2012      | 4.3 GB   |
  | `2008`    | Windows Server 2008      | 3.0 GB   |
  ||||
  | `core11`  | 微型 11 内核             | 2.1 GB   |
  | `tiny11`  | 微型 11                  | 3.8 GB   |
  | `tiny10`  | 微型 10                  | 3.6 GB   |

> [!TIP]
> 要安装 ARM64 版本的 Windows，请使用[ dockur/windows-arm ](https://github.com/dockur/windows-arm/)
