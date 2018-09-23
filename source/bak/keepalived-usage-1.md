---
layout: false
title: keepalived环境配置及使用(1)
description: keepalived环境配置及使用
category: keepalived
---

### keepalived简介
keepalived起初是为LVS设计的，用来监控集群系统中各个服务节点的状态。一旦检测到故障节点，自动将故障节点剔除，故障节点恢复后自动将此节点重新加入到服务集群中。全部自动完成，无需人工干预。后来又加入了VRRP(Virtual Router Redundancy Protocol 虚拟路由器冗余协议)的功能，避免IP单点故障。

### 环境配置
使用两台虚拟机做测试环境，ip分别为:192.168.0.105,192.168.0.107
1.安装并配置操作系统，系统统一为centos7

配置网卡，修改 /etc/sysconfig/network-scripts/ifcfg-xxx 文件xxx为网卡名称。
重启网卡
```shell
ifdown xxx
ifup xxx
```
查看ip配置
```shell
ip addr show
```
修改hostname
```shell
hostnamectl set-hostname xxx
vi /etc/hosts
```

2.关闭SELinux，启用ip转发
```shell
vi /etc/selinux/config ##将SELINUX=enforcing改为SELINUX=disabled 
```
```shell
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p
```

3.安装nginx作为测试的真实服务
```shell
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
sudo yum install nginx
```

4.修改防火墙策略，开放80端口
```shell
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```
开放vrrp组播
```shell
firewall-cmd --direct --permanent --add-rule ipv4 filter INPUT 0 --in-interface xxx --destination 224.0.0.18 --protocol vrrp -j ACCEPT
sudo firewall-cmd --reload
```
* xxx为网卡名称；防火墙不开放vrrp组播会导致脑裂，两个节点都能获取VIP，尝试关闭防火墙导致ping不通vip，原因未知。

5.同步服务器时间
```shell
# 安装ntp服务
yum install ntp
# 开机启动服务
systemctl enable ntpd
# 启动服务
systemctl start ntpd
# 设置亚洲时区
timedatectl set-timezone Asia/Shanghai
# 启用NTP同步
timedatectl set-ntp yes
# 重启ntp服务
systemctl restart ntpd
# 手动同步时间
ntpq -p
```

6.安装keepalived
```shell
sudo yum install keepalived
```

### 配置及测试

1.在节点1，修改文件``/etc/keepalived/keepalived.conf``，内容如下：

```
vrrp_instance VI_1 { ##定义一个虚拟路由器
    state MASTER ## 状态
    interface ens33 ##使用的网络接口
    virtual_router_id 51 ##id，不能大于255
    priority 100 ##优先级
    advert_int 1 ##初始化通告
    authentication { ##认证信息
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress { ##vip信息
        192.168.0.120
    }
}
```
2.启动keepalived

```shell
sudo systemctl start keepalived.service
```
3.查看ip信息``ip addr show``
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:da:ec:e6 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.107/24 brd 192.168.0.255 scope global noprefixroute dynamic ens33
       valid_lft 7070sec preferred_lft 7070sec
    inet 192.168.0.120/32 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::dcc3:cb42:5fee:f65/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

可以看到192.168.0.120这个vip地址已经出现在ens33网卡上，访问192.168.0.120即可访问到107上的nginx服务
4.节点2修改配置，保证两个节点``virtual_router_id`` 和 ``priority`` 不相同，启动节点2的keepalived，

