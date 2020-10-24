---
layout: post
title: 树莓派使用笔记
description: 树莓派安装系统及软件
category: pi
---

### 硬件

型号: 2代B型  1G内存  CPU: Cortex-A7 armv7l  代号:a21041 v1.1

cat /proc/device-tree/model
Raspberry Pi 2 Model B Rev 1.1

### 安装OS

下载系统：
https://www.raspberrypi.org/downloads/raspberry-pi-os/

下载系统写入工具：
https://www.raspberrypi.org/downloads/

默认用户名密码: pi raspberry

开机自动连接wifi

新建文件：`/boot/wpa_supplicant.conf`

```
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
    ssid="TP-LINK-9DD4"
    psk="112233abc"
    key_mgmt=WPA-PSK
    priority=1
}
```

允许 ssh 远程连接，创建文件 `/boot/ssh`

### U盘启动

SD卡和U盘都刷入系统

测试SD卡能正常进入系统

修改SD卡中`/boot/cmdline.txt`文件

root=PARTUUID=da64faf7-02 -> root=/dev/sda2

常用命令

```
blkid #查看块设备信息
mount #查看挂载信息
df -h #查看磁盘空间
```

### 系统初始化

#### 扩展分区

```
sudo raspi-config
```

#### 重装vi

```
sudo apt-get remove vim-common
sudo apt-get install vim
```

#### 启用root账户

1.设置密码 `sudo passwd root`

2.解锁 `sudo passwd --unlock root`

3.允许ssh登录，修改`/etc/ssh/sshd_config`，新增`PermitRootLogin yes`

### 安装软件

```
sudo apt-get install git zsh
```

### 常用命令

1.调试摄像头

```
vcgencmd get_camera #查看摄像头连接情况
```

扩展: `vcgencmd commands` 查看支持的命令

参考: 

https://www.cnblogs.com/ohmybabydoll/p/9886424.html

https://www.lxx1.com/3683

2.安装网络摄像头

https://github.com/barry-ran/raspberry-webcam

https://github.com/waveform80/pistreaming

3.安装aria2

```
apt-get install aria2
```

启动命令: `aria2c --conf-path=/root/disk/etc/aria2.conf -D`






