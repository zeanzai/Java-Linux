# 前言

jdk作为java程序员的开发工具，必不可少。服务器端一般使用基于Hotspot虚拟机的jdk。

# 信息统计

- 下载地址：https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html
- 版本：jdk1.8.0_144
- 安装地址：/usr/setup/jdk1.8.0_144

# 安装

- 上传

- 解压

```
$ tar zxf /opt/packages/jdk-8u144-linux-x64.tar.gz -C /usr/setup
```

- 设置环境变量

```
$ vi /etc/profile

# 添加以下内容：
export JAVA_HOME=/usr/setup/jdk1.8.0_144
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=./:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$PATH:$JAVA_HOME/bin
```

- 使环境变量生效

```
$ source /etc/profile
```

- 测试

```shell
$ java -version
$ java
$ javac
```

# 使用

## 配置
（主要介绍：配置文档地址、配置参数含义、配置参数值的含义、修改过程等）
（待续）

## 调优

参考[jdk调优]()

# 问题解决

- 服务器在安装时，如果没有选中最小化安装，有可能会自带了openjdk，再安装前，需要把openjdk卸载。
