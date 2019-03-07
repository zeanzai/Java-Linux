
- tomcat启动中
- 移动war包到webapps下
- 浏览器中输入
http://10.168.0.120:8080/jenkins
- 初始化安装

- 全局工具设置
  jdk： jdk1.8.0_144
        /usr/setup/jdk1.8.0_144

  maven： apache-maven-3.5.4
          /usr/setup/apache-maven-3.5.4

- 新建任务
  输入任务名->自由风格->丢弃旧的构建->Subversion->轮询SCM（H 2 * * *）->构建（clean package -DskipTests）->执行shell（cp target/*.war /usr/setup/latestTomcat/webapps/）

- 执行构建
  查看控制台输出
  当然是未成功了
  因为没有下载pom中依赖的jar
  需要进入工作区（/usr/setup/apache-tomcat-8.5.32/.jenkins/workspace），手动执行 compile 、 package、clean package等目标（目的是为了下载仓库jar）

- 执行完成之后
  需要将target文件夹删除

- 再次构建
  如果还没有成功，就是因为没有下载好jar，需要执行mvn的相关目标，或将开发环境下面的仓库拷贝到服务器上

- 构建成功
  构建成功，但是tomcat没有启动成功，查看catalina.out 日志
  （日志文件更改所属文件夹的属性）
