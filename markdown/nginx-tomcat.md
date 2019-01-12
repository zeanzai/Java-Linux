# 配置Tomcat的nginx反向代理

## 场景描述
将Tomcat的流量直接通过nginx代理，即使用8101端口监听Tomcat的8080端口

## 配置步骤

- 创建vhost文件夹
在nginx的conf文件夹下创建`vhost`文件夹
- 创建tomcat的配置文件
在`vhost`文件夹下面创建`tomcat-8101.conf`文件，并写入以下内容：
```
server {
	listen       8101;
	server_name localhost;

	location / {
		proxy_pass http://127.0.0.1:8080;
	}
}
```
- 修改nginx的配置文件
在http模块任意处，添加`include vhost/*.conf`
- 重启nginx
输入命令`systemctl restart nginx`
- 开放8101端口
输入命令`firewall-cmd --zone=public --add-port=8101/tcp --permanent`开放8101端口，并输入`firewall-cmd --reload`使防火墙生效【不要忘记关闭8080端口，关闭命令：`firewall-cmd --zone=public --remove-port=8080/tcp --permanent`】
- 测试
浏览器中输入`http://{ip}:8101`进行测试
