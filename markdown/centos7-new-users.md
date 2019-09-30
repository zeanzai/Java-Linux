
# 场景描述

一台服务器上面允许多个用户使用，但是又不能把root账户给非管理人员，为保证安全性，就必须要为普通用户创建子账户，并使用ssh安全登录。
即：

  > 在主机A（ip: 192.168.1.100）上面需要使用主机B（ip: 192.168.1.101）的子账户test登录主机B，要求使用ssh安全方式登录。

# ssh远程登录

## 明文传输与加密传输

### 明文传输

当我们的数据包在网络上传输的时候，以数据包的原始格式进行传输，别人很容易截获我们的数据包，得到我们的信息。

###加密传输

当两个主机之间传输信息或者是A主机远程控制B主机的时候，在两个主机传输数据包之前，加密过之后才通过网络传输过去。因此，就算有人截获了传输的数据包，也不知道传输的内容。

## ssh（secure shell）简介

SSH是建立在传输层和应用层上面的一种安全的传输协议。SSH目前较为可靠，专为远程登录和其他网络提供的安全协议。在主机远程登录的过程中有两种认证方式:

![](../image/centos7-new-users/021.png)

### 基于口令认证

只要你知道自己帐号和口令，就可以登录到远程主机。所有传输的数据都会被加密，但是不能保证你正在连接的服务器就是你想连接的服务器。可能会有别的服务器在冒充真正的服务器，也就是受到“中间人”这种方式的攻击。

### 基于秘钥认证

需要依靠秘钥，也就是你必须为自己创建一对秘钥，并把公用的秘钥放到你要访问的服务器上，客户端软件就会向服务器发出请求，请求用你的秘钥进行安全验证。服务器收到请求之后，现在该服务器你的主目录下寻找你的公用秘钥，然后吧它和你发送过来的公用秘钥进行比较。弱两个秘钥一致服务器就用公用秘钥加密“质询”并把它发送给客户端软件，客户端软件收到质询之后，就可以用你的私人秘钥进行解密再把它发送给服务器。

用这种方式，你必须要知道自己的秘钥口令。但是与第一种级别相比，地为主不需要再网络上传输口令。

第二种级别不仅加密所有传送的数据，而且“中间人”这种攻击方式也是不可能的（因为他没有你的私人密匙）。但是整个登录的过程可能需要10秒。

## 启动ssh服务

### 注意

- centos7 默认启动了ssh服务
- 主机A和主机B都需要开启ssh服务

### 查看是否开发22端口

![](../image/centos7-new-users/017.png)

### 查看ssh服务是否启动

![](../image/centos7-new-users/018.png)

## SSH客户端连接（口令认证）

略

## SSH客户端连接（秘钥认证）

### 在主机B上面生成公钥和私钥

![](../image/centos7-new-users/019.png)

### 主机A拷贝主机b的公钥

![](../image/centos7-new-users/020.png)


### 在主机A上面连接主机B

```shell
[root@studyCentOS7 .ssh]# ssh root@192.168.1.101
Last login: Tue Jun  5 11:20:25 2018 from 10.168.1.223
```

# 步骤

## 创建新用户

```shell
[root@VM_0_6_centos ~]# adduser northmeter
[root@VM_0_6_centos ~]# passwd northmeter
Changing password for user northmeter.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

## 授权

```shell
[root@VM_0_6_centos ~]# ls -l /etc/sudoers
-r--r-----. 1 root root 3938 Jun  7  2017 /etc/sudoers
[root@VM_0_6_centos ~]# chmod -v u+w /etc/sudoers
mode of ‘/etc/sudoers’ changed from 0440 (r--r-----) to 0640 (rw-r-----)
[root@VM_0_6_centos ~]# vi /etc/sudoers
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
northmeter      ALL=(ALL)       ALL

[root@VM_0_6_centos ~]# chmod -v u-w /etc/sudoers
mode of ‘/etc/sudoers’ changed from 0640 (rw-r-----) to 0440 (r--r-----)
```

## 登录

```shell
[root@200 ~]# ssh northmeter@193.112.249.36
northmeter@193.112.249.36's password:
Last login: Tue Jun  5 12:37:45 2018 from 218.17.157.121
```

## 测试

```shell
[root@VM_0_6_centos ~]# su northmeter
[northmeter@VM_0_6_centos root]$ sudo cat /etc/passwd

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for northmeter:
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:998:996:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:997:995::/var/lib/chrony:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
syslog:x:996:994::/home/syslog:/bin/false
centos:x:1000:1000:Cloud User:/home/centos:/bin/bash
northmeter:x:1001:1001::/home/northmeter:/bin/bash
```

> 参考
* [在centos7中添加一个新用户，并授权](https://blog.csdn.net/wangqiuwei07/article/details/75007764)
* [centos7将用户添加到sudo组](https://cloud.tencent.com/info/b6fdf5ea9b40503a31a7f43c8e25e1bf.html)
* [CentOS 7.1下SSH远程登录服务器详解](https://www.linuxidc.com/Linux/2016-03/129204.htm)
