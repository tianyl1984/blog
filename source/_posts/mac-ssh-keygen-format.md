---
layout: post
title: mac使用ssh-keygen生成私钥
description: mac使用ssh-keygen生成私钥方式，及格式相关转换
category: mac
---

mac上可以使用ssh-keygen命令生成私钥，用于连接远程服务器。命令如下：
```
ssh-keygen -t rsa -C "tianyl@x1y2z3.com" -b 4096
```
-t 指定秘钥类型
-C 添加注释
-b 指定秘钥长度

生成的私钥内容大概如下：
```
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBYUHBDJEAu/4vxpg8pp3omiRZgEL726n7UHDJUWNJECNopQrCjmk
....
X60pTqn4Ct59ZYDUEH532K+bHFRztPDBXJPnijw0hu3of+9yy+w==
-----END RSA PRIVATE KEY-----
```

不清楚从哪个版本开始，mac 上生成的格式变成了如下格式：
```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAACFwAAAAdzc2gtcn
....
KOW7H7TbQrLnAAAAEXRpYW55bEB4MXkyejMuY29tAQ==
-----END OPENSSH PRIVATE KEY-----
```

开始结束标记中由 RSA 变成了 OPENSSH，导致部分软件如 DataGrip 之类通过 ssh 连接远程服务器识别秘钥失败，有两种方法可以解决：

#### 方法一：

生成命令添加 -m PEM 指定格式为 PEM 格式
```
ssh-keygen -m PEM -t rsa -C "tianyl@x1y2z3.com" -b 4096
```
这样生成的私钥就是旧的格式，对于已有的私钥可以考虑方法二转换格式。

#### 方法二：

安装 PuTTYgen，转换私钥格式。

安装方式参考官网：https://www.puttygen.com

```
puttygen id_rsa -O private-openssh -o id_rsa.old
```
id_sa 私钥文件
-O 大写英文字母O，指定输出格式
-o 小写英文字母O，指定输出的文件

通过 `puttygen --help` 查看更多其他参数




