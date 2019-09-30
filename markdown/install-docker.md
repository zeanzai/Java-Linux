
## 安装步骤

0. 更新yum包

`yum update`

1. 查看是否安装过旧版本

```
find /etc/systemd -name ‘docker’ -exec rm -f {} \;
find /lib/systemd -name ‘docker’ -exec rm -f {} \;
```

如果安装过旧版本，就进行卸载

```
yum remove docker docker-common docker-selinux docker-engine -y
```


2. 安装依赖软件包

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

> 注意： yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的。

3. 设置yum源

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

此操作会在/etc/yum.repos.d/目录下创建docker-ce.repo文件，里面内容为Docker的存储仓库。感兴趣的同学可以打开看一下。

4. 设置yum镜像

```
sed -i 's+download.docker.com+mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo
yum makecache fast
```

> 上述操作时为了将软件仓库地址替换为 TUNA，国外的镜像安装速度很慢，故采用清华大学 TUNA 镜像源。
> 参考链接： https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/

5. 查看所有仓库中的docker版本

```
yum list docker-ce –showduplicates | sort -r
```

6. 安装docker

```
yum install docker-ce
```

> 注意：
> repo中默认只开启stable仓库。

7. 启动并加入开机自启

```
systemctl enable docker
systemctl start docker
```

## 参考

https://blog.csdn.net/qq_23489303/article/details/80682798
https://blog.csdn.net/aa1215018028/article/details/80623111
