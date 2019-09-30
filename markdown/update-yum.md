
## 说明

yum源是我们下载软件的仓库地址，可以类比着理解为maven管理管理java项目中的依赖的jar。但是centos系统默认的yum源是国外地址，这就造成了，我们在使用yum安装一些软件的时候，需要花费很长的时间去等待。因此，我们需要修改yum源为国内的代理yum源。

## 将yum源修改为国内的yum源

以centos7为例，修改为阿里的yum源
1. 备份本地yum源

`mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak `

2.获取阿里yum源配置文件

 `wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`

也可以使用下面命令进行安装

```
yum-config-manager --add-repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

此操作会在/etc/yum.repos.d/目录下创建Centos-7.repo文件。

3.更新cache

 `yum makecache`

4.查看

 `yum -y update`

> 注意：
>  在第二步获取yum源配置文件时，执行wget命令可能无法执行成功，也可以使用本地机下载好yum源配置文件，然后上传到相关目录下即可。

## 参考

https://www.cnblogs.com/xjh713/p/7458437.html
