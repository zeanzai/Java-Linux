# 前言

（主要介绍：应用场景，大概发展历史等。）

（读者在写自己的安装文档时，此部分可以不用记录。）

# 信息统计

（主要介绍：下载地址、软件版本、安装地址、配置文档地址、日志文档地址、占用端口、使用地址、用户信息、测试安装结果、其他有用信息等）

# 安装并使用nexus2.x

## 安装

- 安装依赖

```
$ java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)
```

- 上传到`/opt/package`目录

- 解压到解压目录

```
$ tar zxf /opt/package/nexus-2.14.5-02-bundle.tar.gz -C /usr/setup/
```

- 移动

```
$ mv -f /usr/setup/sonatype-work/ /opt/
```

- 配置nexus

```
$ vi /usr/setup/nexus-2.14.5-02/conf/nexus.properties
// 修改后：
application-host=10.168.0.120
nexus-work=/opt/sonatype-work/nexus

$ vi /usr/setup/nexus-2.14.5-02/bin/nexus
// 修改后：
NEXUS_HOME="/usr/setup/nexus-2.14.5-02"
RUN_AS_USER=root

$ vi /usr/setup/nexus-2.14.5-02/bin/jsw/conf/wrapper.conf
// 修改后：
wrapper.java.command=/usr/setup/jdk1.8.0_144/bin/java
```

- 开放端口

```
$ firewall-cmd --zone=public --permanent --add-port=8081/tcp
$ firewall-cmd --reload
```

- 设置服务

```
$ vi /etc/systemd/system/nexus.service
[Unit]
Description=nexus
After=network.target
[Service]
Type=forking
ExecStart=/usr/setup/nexus-2.14.5-02/bin/nexus start
ExecReload=/usr/setup/nexus-2.14.5-02/bin/nexus restart
ExecStop=/usr/setup/nexus-2.14.5-02/bin/nexus stop
PrivateTmp=true
[Install]
WantedBy=multi-user.target

$ systemctl enable nexus
$ systemctl start nexus
```

- 测试

```
浏览器输入：http://10.168.0.120:8081/nexus
```

## 使用nexus2.x

### 权限管理

1. 用户
2. 角色

### 仓库类型

几种仓库类型的简单介绍

### 查看日志

如何查看nexus的执行日志

### 下载中央仓库索引

1. 设置仓库开关
2. 创建任务

### 设置项目连接私服

- 单个项目连接私服

```
// 只需要在项目的pom文件中添加下面内容即可

<repositories>
    <repository>
        <id>Nexus</id>
        <name>10.168.0.120-Nexus</name>
        <url>http://10.168.0.120:8081/nexus/content/groups/public/</url>
    </repository>
</repositories>

```

- 全局连接私服

```
// 在maven的setting.xml文件中配置下面内容即可

<mirrors>
    <mirror>
        <id>devnexus</id>
        <name>devnexus</name>
        <mirrorOf>*</mirrorOf>
        <url>http://10.168.0.120:8081/nexus/content/groups/public/</url>
    </mirror>
</mirrors>
```

### 本地开发的jar发布到nexus上面

- 第一步：配置setting.xml文件

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">


    <localRepository>D:\maven\my_local_repository</localRepository>

    <pluginGroups>
    </pluginGroups>

    <proxies>
    </proxies>


    <!--设置 Nexus 认证信息-->
    <servers>
        <server>
            <id>nexus-releases</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
        <server>
            <id>nexus-snapshots</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
    </servers>


    <!--设置 Nexus 镜像，后面只要本地没对应的以来，则到 Nexus 去找-->
    <mirrors>
        <mirror>
            <id>nexus-releases</id>
            <mirrorOf>*</mirrorOf>
            <url>http://10.168.0.120:8081/repository/maven-releases/</url>
        </mirror>
        <mirror>
            <id>nexus-snapshots</id>
            <mirrorOf>*</mirrorOf>
            <url>http://10.168.0.120:8081/repository/maven-snapshots/</url>
        </mirror>
        <mirror>
            <id>maven-aliyun</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>

    <profiles>
        <profile>
            <id>nexus</id>
            <repositories>
                <repository>
                    <id>nexus-releases</id>
                    <url>http://nexus-releases</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
                <repository>
                    <id>nexus-snapshots</id>
                    <url>http://nexus-snapshots</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>nexus-releases</id>
                    <url>http://nexus-releases</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
                <pluginRepository>
                    <id>nexus-snapshots</id>
                    <url>http://nexus-snapshots</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
    </profiles>

    <activeProfiles>
        <activeProfile>nexus</activeProfile>
    </activeProfiles>

</settings>
```

- 第二步：在pom文件中添加：

```
<distributionManagement>
    <repository>
        <id>nexus-releases</id>
        <url>http://10.168.0.120:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <url>http://10.168.0.120:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

### 附：java项目不同模块的划分

划分的背景
划分方法
参考地址

# 安装并使用nexus3.x

## 安装

## 使用

# 参考链接
1. https://my.oschina.net/u/3268478/blog/1802385
2. https://blog.csdn.net/plei_yue/article/details/78616267
3. https://blog.csdn.net/zwjzqqb/article/details/81299341
4. https://blog.csdn.net/sunggff/article/details/79354359
5. https://blog.csdn.net/u013030276/article/details/79640115
6. http://agiledon.github.io/blog/2013/11/10/create-two-jars-from-one-project-using-maven/
7. http://ju.outofmemory.cn/entry/69763
