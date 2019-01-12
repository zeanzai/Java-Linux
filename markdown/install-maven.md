# 前言
maven作为java后端管理项目依赖的重要工具，在服务器端也经常用来完成自动化构建等任务。

# 信息统计
下载地址：http://mirror.bit.edu.cn/apache/maven/maven-3/
软件版本：apache-maven-3.5.4-bin
安装地址：/usr/setup/apache-maven-3.5.4
配置文件地址： /usr/setup/apache-maven-3.5.4/conf/settings.xml

# 安装步骤
- 解压
```shell
tar -zxf apache-maven-3.5.4-bin.tar.gz -C /usr/setup/
```

- 配置环境变量
```shell
vi /etc/profile
export MAVEN_HOME=/usr/setup/apache-maven-3.5.4
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
```

- 使环境变量生效
```shell
source /etc/profile
```
- 测试
```shell
mvn -version
```

- 修改仓库地址
```shell
mkdir /opt/repository

vi /usr/setup/apache-maven-3.5.4/conf/settings.xml
<localRepository>/opt/repository</localRepository>
```
