

# Centos7上面tomcat的日志分割

# 前言

笔者在进行试验时，有以下几个操作习惯，具体[参考](/post/dev/how-to-devops/)。

Centos7默认开通了80端口和22端口。

# 应用场景
tomcat7 在实际生产环境中，由于项目不断部署，Catalina.out文件达到了2G的大小，并且每次查询日志的时候需要查询整个文件，造成查询起来过于困难。

希望：Catalina.out日志文件以日期划分。放置的位置为/home/logs/tomcat7目录下面。如/home/logs/tomcat7/20180807/catalina.out。

# 关闭tomcat


# 下载相关的java
**Log4j 不支持按日期生成不同的文件夹**

tomcat-juli.jar 和 tomcat-juli-adapters.jar ： http://www.apache.org/dist/tomcat/tomcat-7/v7.0.90/bin/extras/

log4j-1.2.17.jar : http://www.apache.org/dist/logging/log4j/1.2.17/
(先下载zip，然后解压，找到jar)

# 上传jar
1. 将上述三个jar放入$CATALINA_HOME/lib下面
2. 更改$CATALINA_HOME/bin下面的tomcat-juli.jar为tomcat-juli_bak.jar
3. 将tomcat-juli.jar放入$CATALINA_HOME/bin目录下
4. 备份$CATALINA_HOME/conf/logging.properties文件为logging_bak.properties

# 制作资源文件
文件名字为：log4j.properties，并将改文件上传至：$CATALINA_HOME/lib目录下
```
log4j.rootLogger=INFO, CATALINA

log4j.appender.CATALINA=org.apache.log4j.DailyRollingFileAppender
log4j.appender.CATALINA.File=/home/logs/tomcat7/catalina
log4j.appender.CATALINA.Append=true
log4j.appender.CATALINA.Encoding=UTF-8
log4j.appender.CATALINA.DatePattern='.'yyyy-MM-dd'.log'
log4j.appender.CATALINA.layout=org.apache.log4j.PatternLayout
log4j.appender.CATALINA.layout.ConversionPattern=%d [%t] %-5p %c- %m%n

log4j.appender.LOCALHOST=org.apache.log4j.DailyRollingFileAppender
log4j.appender.LOCALHOST.File=/home/logs/tomcat7/localhost
log4j.appender.LOCALHOST.Append=true
log4j.appender.LOCALHOST.Encoding=UTF-8
log4j.appender.LOCALHOST.DatePattern='.'yyyy-MM-dd'.log'
log4j.appender.LOCALHOST.layout=org.apache.log4j.PatternLayout
log4j.appender.LOCALHOST.layout.ConversionPattern=%d [%t] %-5p %c- %m%n

log4j.appender.MANAGER=org.apache.log4j.DailyRollingFileAppender
log4j.appender.MANAGER.File=/home/logs/tomcat7/manager
log4j.appender.MANAGER.Append=true
log4j.appender.MANAGER.Encoding=UTF-8
log4j.appender.MANAGER.DatePattern='.'yyyy-MM-dd'.log'
log4j.appender.MANAGER.layout=org.apache.log4j.PatternLayout
log4j.appender.MANAGER.layout.ConversionPattern=%d [%t] %-5p %c- %m%n

log4j.appender.HOST-MANAGER=org.apache.log4j.DailyRollingFileAppender
log4j.appender.HOST-MANAGER.File=/home/logs/tomcat7/host-manager
log4j.appender.HOST-MANAGER.Append=true
log4j.appender.HOST-MANAGER.Encoding=UTF-8
log4j.appender.HOST-MANAGER.DatePattern='.'yyyy-MM-dd'.log'
log4j.appender.HOST-MANAGER.layout=org.apache.log4j.PatternLayout
log4j.appender.HOST-MANAGER.layout.ConversionPattern=%d [%t] %-5p %c- %m%n

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.Encoding=UTF-8
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d [%t] %-5p %c- %m%n

log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost]=INFO, LOCALHOST
log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/manager]=INFO, MANAGER
log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager]=INFO, HOST-MANAGER
```

# 修改配置
$CATALINA_HOME/conf/context.xml文件中的<Context>标签为：
`<Context swallowOutput="true">`

# 重启



# 参考
https://tomcat.apache.org/tomcat-7.0-doc/logging.html#Using_Log4j
