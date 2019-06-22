---
layout: post
title: keepalived环境配置及使用(2)
description: keepalived环境配置及使用
category: keepalived
---

### 问题
1. 如果后端服务例如nginx意外关闭，ip地址如何切换？
2. 主从节点如何做负载？

### vrrp_script
keepalived默认可以对网络或进程本身的监控，比如：网络出现故障，服务器宕机，keepalived异常退出时，自动切换。但是这些往往还是不够的，业务进程出现问题为了保证高可用也需要切换，比如nginx进程异常退出之类。这时，可以通过编写脚本对业务进程进行检测，出现问题进行切换。

#### 定义检测项

keepalived.conf中新增以下配置

```shell
vrrp_script chk_nginx {
    script "killall -0 nginx"
    interval 1
    weight -2
}
```

script：检测时执行的脚本，返回0表示检测成功，非0表示失败。`killall -0 nginx`并非杀死nginx，而是检测nginx进程是否存在
interval：检测周期
weight：权重变化。为正值时检测成功priority增加此值；为负值时检测失败priority减掉weight绝对值

#### 配置检测

```shell
vrrp_instance VI_1 {
    ...
    track_script {
        chk_nginx
    }
}
```

可以配置多个检查项

### 

### 参考资料

[keepalived之vrrp_script详解](https://www.cnblogs.com/arjenlee/p/9258188.html)

[使用LVS实现负载均衡原理及安装配置详解](https://www.cnblogs.com/liwei0526vip/p/6370103.html)

https://my.oschina.net/agiledev/blog/1975543


