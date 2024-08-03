#  GitHub加速方法

#  方法一：

首先你需要有一个可以直连的sk5代理，建议用外国服务器安装xui面板自建
```
sudo vim ~/.bashrc
```
```
export all_proxy="socks5://user:pass@127.0.0.1:20170"
export HTTP_PROXY="socks5://user:pass@127.0.0.1:20170"
export HTTPS_PROXY="socks5://user:pass@127.0.0.1:20170"
```
```
source ~/.bashrc
```

此时输入```curl ip.sb```查看本机IP判断是否配置成功

如需删除并停止，只需清空文件并重新加载即可

注意：如果脚本命令前面带`sudo`，则需要添加`-E`传递环境变量才会生效




---

# 方法二：v2rayA代理

#### [官方文档](https://v2raya.org/docs/prologue/installation/debian/)

#### 手动使用软件源安装
[手动下载v2rayA软件源](https://github.com/v2rayA/v2raya-apt/tree/master/pool/main/v/v2raya)上传到服务器

[手动下载xray软件源](https://github.com/v2rayA/v2raya-apt/tree/master/pool/main/x/xray)上传到服务器

安装
```
sudo apt install xray.deb路径 v2rayA.deb路径
```
替换两个deb包所在的实际路径

启动
```
sudo systemctl start v2raya.service
```
```
默认端口：
2017: v2rayA面板端口
20170: SOCKS协议
20171: HTTP协议
20172: 带分流规则的HTTP协议
32345: tproxy透明代理所需
```

卸载
```
sudo apt-get remove v2raya xray
```



### 快捷安装方式
v2ray内核
```
sudo apt install snapd
```
```
sudo snap install v2raya
```
需要修改配置文件
```
sudo vim /etc/systemd/system/snap.v2raya.v2raya.service
```
`ExecStart`参数后面增加` --address 0.0.0.0:2017`，只需增加即可，注意空格需保留。

加载并重启
```
sudo systemctl daemon-reload
sudo systemctl restart snap.v2raya.v2raya.service
```

卸载
```
sudo snap remove v2raya
```
### win系统安装
以管理员身份运行PowerShell
```
winget install --id v2rayA.v2rayA
```

会自动安装到桌面，启动后自动配置系统代理，端口为`52345`


部分应用（比如命令行程序）可能不读取或者不使用系统代理，你可能需要`proxychains`来强行让它们走代理，或者使用程序自身的代理配置。

如果 v2rayA 意外退出，那么 v2rayA 无法在退出的时候帮你取消系统代理，这种情况下你需要自行去`Internet`选项或者系统设置里面关掉代理。


更新
```
winget upgrade --id v2rayA.v2rayA
```
卸载
```
winget uninstall --id v2rayA.v2rayA
```

开机自启

将你`scoop\shims`目录下的`start-v2raya.cmd`复制到`启动`文件夹（一般位于 `C:\Users\YourUserName\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`）。

命令运行示例：
```
Copy-Item -Path '~\scoop\shims\start-v2raya.cmd' -Destination '~\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup'
```


---

---

## 国内Debian / Ubuntu切换apt源教程

#### Github一键换源脚本
```
bash <(curl -sSL https://raw.githubusercontent.com/SuperManito/LinuxMirrors/main/ChangeMirrors.sh)
```
国内Gitee一键换源脚本
```
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```


#### 手动换源
需要root权限

先备份`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

打开文件`/etc/apt/sources.list`

清华大学`debian 11`源
```
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
```

清华大学`ubuntu 20.04`源

```
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

## Pre-released source, not recommended.
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

官方`debian11`源
```
deb https://deb.debian.org/debian/ bullseye main contrib non-free
deb-src https://deb.debian.org/debian/ bullseye main contrib non-free

deb https://deb.debian.org/debian/ bullseye-updates main contrib non-free
deb-src https://deb.debian.org/debian/ bullseye-updates main contrib non-free

deb https://deb.debian.org/debian/ bullseye-backports main contrib non-free
deb-src https://deb.debian.org/debian/ bullseye-backports main contrib non-free

deb https://deb.debian.org/debian-security/ bullseye-security main contrib non-free
deb-src https://deb.debian.org/debian-security/ bullseye-security main contrib non-free
```

更新源
```
sudo apt update
```

切换完成


---
### 配置 APT 包管理器的代理服务器设置

编辑`/etc/apt/apt.conf.d/proxy.conf`

```
Acquire::http::Proxy "socks5h://username:password@127.0.0.1:1080";
Acquire::https::Proxy "socks5h://username:password@127.0.0.1:1080";
Acquire::socks::Proxy "socks5h://username:password@127.0.0.1:1080";
```
```
sudo apt update
```

---

###  方法三：修改系统hosts文件实现加速(老方法不稳定)：

+ 文件路径：
```
windows: C:\Windows\System32\drivers\etc 
linux: /etc/hosts
```
+ 格式：
```
127.0.0.11 github.com
127.0.0.11 raw.githubusercontent.com
127.0.0.11 assets-cdn.github.com
127.0.0.11 github.global.ssl.fastly.net
```
把```127.0.0.11```替换为查询到的地址

可以从这里获取最新可用的hosts地址：https://github.com/521xueweihan/GitHub520/blob/main/hosts

该内容会每天自动更新。

#### 修改 hosts 文件

hosts 文件在每个系统的位置不一，详情如下：
- Windows 系统：`C:\Windows\System32\drivers\etc\hosts`
- Linux 系统：`/etc/hosts`
- Mac（苹果电脑）系统：`/etc/hosts`
- Android（安卓）系统：`/system/etc/hosts`
- iPhone（iOS）系统：`/etc/hosts`

修改方法，把上面的内容复制到文本末尾：

1. Windows 使用记事本
2. Linux、Mac 使用 Root 权限：`sudo nano /etc/hosts`
3. iPhone、iPad 须越狱、Android 必须要 root

####  激活生效
大部分情况下是直接生效，如未生效可尝试下面的办法，刷新 DNS：

1. Windows：在CMD窗口输入：`ipconfig /flushdns`

2. Linux命令：`sudo systemctl restart nscd` 如报错则须安装：`sudo apt install nscd` 或 `sudo /etc/init.d/nscd restart`

3. Mac命令：`sudo killall -HUP mDNSResponder`

**PS：** 上述方法无效可以尝试重启机器。

###  GitHub加速网站（不会使用请谷歌） 

https://www.jsdelivr.com/github

https://ghproxy.agrayman.gay

https://gh.api.99988866.xyz

https://mirror.ghproxy.com

https://gitclone.com

https://hub.nuaa.cf

https://github.welab.eu.org

https://ghps.cc
