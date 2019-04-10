

1. 下载依赖包，并将依赖包放置到Tomcat的lib库内
cd /usr/setup/latestTomcat/lib
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.32/bin/extras/catalina-jmx-remote.jar
chown tomcat: catalina-jmx-remote.jar

2. 创建用户和密码文件
vi /usr/setup/latestTomcat/conf/jmxremote.access
添加： admin readwrite

vi /usr/setup/latestTomcat/conf/jmxremote.password
添加:admin admin

chown tomcat: jmxremote.*

3. 修改配置文件
vi /usr/setup/latestTomcat/conf/server.xml

<Server port="8005" shutdown="SHUTDOWN">
	<!-- 添加以下内容 -->
	<Listener className="org.apache.catalina.mbeans.JmxRemoteLifecycleListener" rmiRegistryPortPlatform="10001" rmiServerPortPlatform="10002" />
</Server>

4. 修改环境变量
vi /usr/setup/latestTomcat/bin/setenv.sh
添加以下内容：
JAVA_OPTS="-Dcom.sun.management.jmxremote.ssl=false
  -Dcom.sun.management.jmxremote.password.file=/usr/setup/latestTomcat/conf/jmxremote.password
  -Dcom.sun.management.jmxremote.access.file=/usr/setup/latestTomcat/conf/jmxremote.access
  -Djava.rmi.server.hostname=10.168.1.200"

chown tomcat: /usr/setup/latestTomcat/bin/setenv.sh

5. 开放端口
firewall-cmd --add-port=10001/tcp --permanent
firewall-cmd --add-port=10002/tcp --permanent
firewall-cmd --reload

6. 重启Tomcat
systemctl restart tomcat

7. 打开visualvm
%JAVA_HOME%\bin\jvisualvm.exe

8. 右键Remote
输入ip

9. 右键jmx
输入端口、用户名、密码、勾选保存证书和不使用ssl

10. 参考
https://my.oschina.net/weidedong/blog/787203
