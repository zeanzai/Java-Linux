
## Centos7修改Dns

https://www.cnblogs.com/dadadechengzi/p/6670530.html

在CentOS 7下，手工设置 /etc/resolv.conf 里的DNS，过了一会，发现被系统重新覆盖或者清除了。和CentOS 6下的设置DNS方法不同，有几种方式：

1、使用全新的命令行工具 nmcli 来设置
```
// 显示当前网络连接
$ nmcli connection show
NAME UUID                                 TYPE           DEVICE
eno1 5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03 802-3-ethernet eno1

// 修改当前网络连接对应的DNS服务器，这里的网络连接可以用名称或者UUID来标识
$ nmcli con mod eno1 ipv4.dns "114.114.114.114 8.8.8.8"

// 将dns配置生效
$ nmcli con up eno1
```

2、使用传统方法，手工修改 /etc/resolv.conf
```
// 修改 /etc/NetworkManager/NetworkManager.conf 文件，在main部分添加 “dns=none” 选项：

[main]
plugins=ifcfg-rh
dns=none

// NetworkManager重新装载上面修改的配置
$ systemctl restart NetworkManager.service

// 手工修改 /etc/resolv.conf
nameserver 114.114.114.114
nameserver 8.8.8.8
```
