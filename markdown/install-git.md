- 查看yum库中的版本：

```
yum info git
```

- 查看是否安装

```
git --version
```

- 安装依赖包

```
yum install -y curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
```

- 查看是否安装

```
git --version
```

- 卸载旧版本

```
yum remove git
```

- 下载 https://github.com/git/git/releases

- 上传到制定目录

- 解压

```
tar zxf git-2.18.0.tar.gz
```

- 安装

```
make prefix=/usr/setup/git_2.18.0 all
make prefix=/usr/setup/git_2.18.0 install
```

- 配置环境变量

```
vi /etc/profile

export GIT_HOME=/usr/setup/git_2.18.0
export PATH=$PATH:$GIT_HOME/bin
source /etc/profile
```

- 查看是否安装

```
git --version
```

- 配置

```
git config --global user.name "zeanzai"
git config --global user.email "438123371@qq.com"
```
