# 系统信息查看

## 版本

```
$ uname -a

```

## cpu个数及核数

```
总核数 = 物理CPU个数 X 每颗物理CPU的核数
总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

# 全部信息
$ cat /proc/cpuinfo

# 物理CPU个数
$ cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 每个物理CPU中core的个数(即核数)
$ cat /proc/cpuinfo| grep "cpu cores"| uniq

# 逻辑CPU的个数
$ cat /proc/cpuinfo| grep "processor"| wc -l
```

## 计算机位数

```
$ getconf LONG_BIT
```

## 计算机名

```
$ hostname
```

## 环境变量

```
$ env
```

## 系统运行了多长时间

```
$ uptime
```

1. 当前时间`10:55:01`
2. 系统运行了多少时间,1:28（1小时28分)
3. 多少个用户,2 users
4. 平均负载：0.00, 0.01, 0.05，最近1分钟、5分钟、15分钟系统的负载

# 资源

## 内存

```
$ cat /proc/meminfo
$ free -m
```

1. 查看总内存情况使用命令`cat /proc/meminfo`即可。
2. 查看内存占用状态使用`free -m`(-m，单位是m，如果是-g，单位是g）。

我们通过free命令查看机器空闲内存时，会发现free的值很小。这主要是因为，在linux中有这么一种思想，内存不用白不用，因此它尽可能的cache和buffer一些数据，以方便下次使用。但实际上这些内存也是可以立刻拿来使用的。

# 硬盘

## 分区使用情况

```
$ df -h
```

## 指定目录大小

```
$ du -sh <目录名>
```

## 所有分区

```
$ fdisk -l
```

## 所有交换分区

```
$ swapon -s
```

# 网络

```
// 所有网络接口属性
$ ifconfig

// 带宽
$ ethtool 网卡名

// 路由表
$ route -n

// 所有监听端口
$ netstat -lntp

// 所有已经建立的连接
$ netstat -antp
```

# 进程

```
// 所有进程
$ ps -ef

// 查看进程占用cpu资源大小
$ ps -aux

// 实时显示进程状态
$ top
```

# 用户

```
// 查看系统所有用户
$ cut -d: -f1 /etc/passwd

// 查看活动用户
$ w

// 查看用户登录日志
$ last
```

# 参考
1. https://blog.csdn.net/u012965373/article/details/76172581
2. https://www.cnblogs.com/zy-plan/p/8617202.html
