## immortalwrt

在线编译地址：https://firmware-selector.immortalwrt.org

## 必备软件
```
luci-app-openclash luci-theme-argon luci-app-homeproxy luci-i18n-homeproxy-zh-cn luci-app-ttyd luci-i18n-ttyd-zh-cn
```

## 初始化构建脚本
```
#!/bin/sh
exec >/tmp/setup.log 2>&1

###########################################################
#                  自 定 义 配 置 区 域
###########################################################

### LuCI 默认主题（需要固件中已下载该主题）
luci_theme="argon"

### 系统后台密码（为空则不修改）
root_password="root"

### LAN 的 IPv4 地址（也是后台地址，例如 192.168.2.1）
lan_ip_address="192.168.2.1"

### LAN 的子网掩码（例如 255.255.255.0）
# lan_netmask="255.255.255.0"

### LAN 的 IPv4 网关（可为空）
# lan_gateway="192.168.1.1"

### LAN 的 DNS（多个 DNS 可空格分隔，如 "8.8.8.8 1.1.1.1"）
# lan_dns="8.8.8.8 223.5.5.5"

### DHCP 是否开启（1=开启，0=关闭）
# lan_dhcp_enable="1"

### DHCP 起始地址
# lan_dhcp_start="100"

### DHCP 地址池数量
# lan_dhcp_limit="150"

### DHCP 租约时间
# lan_dhcp_leasetime="12h"

### WiFi 名称 SSID（为空则不修改）
# wlan_name="ImmortalWrt"

### WiFi 密码（≥ 8 位才生效）
# wlan_password="12345678"

### PPPoE 宽带账号（为空则跳过）
# pppoe_username=""

### PPPoE 宽带密码
# pppoe_password=""

###########################################################
#                  正 式 配 置 流 程
###########################################################

# ------------ root 密码 ------------
if [ -n "$root_password" ]; then
  (echo "$root_password"; sleep 1; echo "$root_password") | passwd >/dev/null
fi

# ------------ LAN 基础配置 ------------
if [ -n "$lan_ip_address" ]; then
  uci set network.lan.ipaddr="$lan_ip_address"
fi

if [ -n "$lan_netmask" ]; then
  uci set network.lan.netmask="$lan_netmask"
fi

if [ -n "$lan_gateway" ]; then
  uci set network.lan.gateway="$lan_gateway"
fi

# DNS
if [ -n "$lan_dns" ]; then
  uci delete network.lan.dns 2>/dev/null
  for d in $lan_dns; do
    uci add_list network.lan.dns="$d"
  done
fi

uci commit network

# ------------ DHCP 设置 ------------
if [ -n "$lan_dhcp_enable" ]; then
  uci set dhcp.lan.ignore=$([ "$lan_dhcp_enable" = "1" ] && echo 0 || echo 1)
fi

[ -n "$lan_dhcp_start" ] && uci set dhcp.lan.start="$lan_dhcp_start"
[ -n "$lan_dhcp_limit" ] && uci set dhcp.lan.limit="$lan_dhcp_limit"
[ -n "$lan_dhcp_leasetime" ] && uci set dhcp.lan.leasetime="$lan_dhcp_leasetime"

uci commit dhcp

# ------------ WIFI 配置 ------------
if [ -n "$wlan_name" ] && [ -n "$wlan_password" ] && [ ${#wlan_password} -ge 8 ]; then
  uci set wireless.@wifi-device[0].disabled='0'
  uci set wireless.@wifi-iface[0].disabled='0'
  uci set wireless.@wifi-iface[0].encryption='psk2'
  uci set wireless.@wifi-iface[0].ssid="$wlan_name"
  uci set wireless.@wifi-iface[0].key="$wlan_password"
  uci commit wireless
fi

# ------------ PPPoE 宽带拨号 ------------
if [ -n "$pppoe_username" ] && [ -n "$pppoe_password" ]; then
  uci set network.wan.proto=pppoe
  uci set network.wan.username="$pppoe_username"
  uci set network.wan.password="$pppoe_password"
  uci commit network
fi

# ------------ LuCI 主题设置 ------------
if [ -n "$luci_theme" ]; then
  # 设置默认主题
  uci set luci.main.mediaurlbase="/luci-static/$luci_theme"
  uci commit luci
fi

echo "All done!"
```

## VirtualBox虚拟机运行immortalwrt文档

1：选择`x86/64`型号，编译后，下载`COMBINED (EXT4)`格式的镜像，并解压到下载目录

LAN口 IP地址记得改成`192.168.56.2`

2：打开CMD终端

- 进入镜像文件目录
```
cd C:\Users\admin\Downloads
```
- 将镜像转换成VDI格式
```
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" convertfromraw immortalwrt-24.10.4-f726c678216d-x86-64-generic-ext4-combined.img immortalwrt.vdi --format VDI
```

3：打开VirtualBox，新建虚拟机，OS选择`Linux`，然后选择`Other Linux`，然后指定虚拟硬盘，选择`使用已有的虚拟硬盘文件`，选择`immortalwrt.vdi`文件，然后点击完成，然后设置里找到网络，选择`Host-Only`网络启动，勾选`Virtual Cable Connected`

> 如果你电脑是插网线的，则虚拟机可以直接选择桥接网卡，连wifi的不行

4：浏览器进入`192.168.56.2`，密码是下面脚本中设置的`root`
