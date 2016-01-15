---
layout: page
title: F&Q
permalink: /faq/
---

F&Q原本写作FAQ ，是Frequently Asked Questions的首字母构成的缩略词。

**(1) 问：如何让SSH保持长连接？**

**答：** 首先：修改配置文件 /etc/ssh/sshd_config 加入以下两个参数。

```shell
TCPKeepAlive yes
ServerAliveInterval 60 # 时间单位为秒，多少秒自动请求一次
```
然后：重启ssh服务

```shell
/etc/init.d/ssh restart
```
**(2) 问：SMTP和POP和IMAP的作用是什么？POP和IMAP有什么不用？**

**答：** 简单可以这么理解，SMTP是用来发送邮件的，POP/IMAP是用来接收邮件的。其中采用POP协议，客户端对邮件的删改操作不能和服务器端同步，而使用IMAP协议是可以同步的。

**(3) 问：Git http方式如何记住密码？**

**答：** 输入密码前先执行如下命令，再次提交的时候就不需要输入密码了。

```shell
git config --global credential.helper store
```

**(4) 问：Ubuntu编译PHP报错怎么办？**

**答：** 查看./configuer --.....的最后一行缺少什么，然后缺什么补什么就可以。一般都是缺少类库导致的。

**(5) 问：Ubuntu14.04 如何跟换源？**

**答：** 首先备份官网字段的源，然后替换国内的速度比较稳定的源。

```shell
# 备份
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
# 先删除sources.list里面所有的内容，然后加入适当的国内的源
sudo vim /etc/apt/sources.list
deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
```

**(6) 问：scp 命令如何使用？**

**答：** 首先记住sftp，ssh，scp中的s都是secure的意思也就是“安全”的意思。其中 ```-P```是ssh端口（默认22可以不加），```－v```显示进程，其实还有```-4```，```-6```指定ipv4，ipv6的选项。

```shell
# 从本地到远程。本地当前目录下的test.log复制到远程主机的/home/test/目录。
scp -v -P 22  ./test.log test@xxx.xxx.xxx.xxx:/home/test/
# 从远程到本地。远程主机的/home/test/目录下的test.log复制本地当前目录。
scp -v -P 22 test@xxx.xxx.xxx.xxx:/home/test/test.log ./
```

**(7) 问：Ubuntu如何限定端口？**

**答：** 使用```ufw```。 

```shell
#开启防火墙
sudu ufw enable
#关闭防火墙
sudu ufw disable
#新增
sudo ufw allow 端口号
#删除添加过的
sudo ufw delete allow 端口号
```


{% include extends/disqus.html %}



