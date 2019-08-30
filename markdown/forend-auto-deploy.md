# 安装步骤
- 上传到指定目录
- 解压到安装目录
- 配置环境变量

```
export NODE_HOME=/usr/setup/node-v10.14.2-linux-x64
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin:$NODE_HOME/bin

```

- 使配置文件生效
```
source /etc/profile
```

# 构建前端项目
- 安装node.js pluins插件
- 配置全局工具
- 配置构建

```
rm -rf /usr/setup/nginx-1.14.1/html/machine-cloud/
cnpm install node-sass
cnpm run build
cd ./dist/
cp -rf ./* /usr/setup/nginx-1.14.1/html/machine-cloud/
cd ../
rm -rf ./*
```

# 配置Nginx
- 将nginx.conf的访问用户修改为root
```
# use nobody 改为 use root
```
- 配置machine-cloud的包的own为tomcat，这样就在拷贝文件时不会出现没有权限错误
```
chown -R tomcat: ./machine-cloud
```
