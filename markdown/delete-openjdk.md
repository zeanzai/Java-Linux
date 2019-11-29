
## centos7上删除默认jdk

```shell
// 查看Java
[root@localhost local]# java -version
openjdk version "1.8.0_101"
OpenJDK Runtime Environment (build 1.8.0_101-b13)
OpenJDK 64-Bit Server VM (build 25.101-b13, mixed mode)

// 查看有哪些安装包
[root@localhost northmeter]#  rpm -qa | grep java
javapackages-tools-3.4.1-11.el7.noarch
java-1.8.0-openjdk-1.8.0.101-3.b13.el7_2.x86_64
python-javapackages-3.4.1-11.el7.noarch
tzdata-java-2016f-1.el7.noarch
java-1.7.0-openjdk-headless-1.7.0.111-2.6.7.2.el7_2.x86_64
java-1.8.0-openjdk-headless-1.8.0.101-3.b13.el7_2.x86_64
java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64

// 卸载
[root@localhost northmeter]# rpm -e --nodeps java-1.8.0-openjdk-1.8.0.101-3.b13.el7_2.x86_64
[root@localhost northmeter]# rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.111-2.6.7.2.el7_2.x86_64
警告：/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64/jre/lib/security/local_policy.jar 已另存为 /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64/jre/lib/security/local_policy.jar.rpmsave
警告：/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64/jre/lib/security/US_export_policy.jar 已另存为 /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64/jre/lib/security/US_export_policy.jar.rpmsave
[root@localhost northmeter]# rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.101-3.b13.el7_2.x86_64
[root@localhost northmeter]# rpm -e --nodeps java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64

// 删除完毕，测试
[root@localhost northmeter]#  rpm -qa | grep java
javapackages-tools-3.4.1-11.el7.noarch
python-javapackages-3.4.1-11.el7.noarch
tzdata-java-2016f-1.el7.noarch
[root@localhost northmeter]# java -version
-bash: /usr/bin/java: 没有那个文件或目录
[root@localhost northmeter]# java
-bash: /usr/bin/java: 没有那个文件或目录
[root@localhost northmeter]# javac
bash: javac: 未找到命令...
[root@localhost northmeter]#
```
