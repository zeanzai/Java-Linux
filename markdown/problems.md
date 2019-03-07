
# selinux
- 查看
  getenforce 或 /usr/sbin/sestatus -v

- 临时关闭
  setenforce 0

- 永久关闭
  vi /etc/selinux/config
  SELINUX=disabled

- 查看Linux状态
  sestatus

- selinux的知识点
  https://blog.csdn.net/yanjun821126/article/details/80828908


# yum安装软件时报错libmysqlclient.so.18()(64bit)
- 问题描述
  使用yum安装软件时报错：
    2:postfix-2.10.1-6.el7.x86_64 has missing requires of libmysqlclient.so.18()(64bit)
    2:postfix-2.10.1-6.el7.x86_64 has missing requires of libmysqlclient.so.18(libmysqlclient_18)(64bit)
- 解决办法：
  wget http://www.percona.com/redir/downloads/Percona-XtraDB-Cluster/5.5.37-25.10/RPM/rhel6/x86_64/Percona-XtraDB-Cluster-shared-55-5.5.37-25.10.756.el6.x86_64.rpm

  rpm -ivh Percona-XtraDB-Cluster-shared-55-5.5.37-25.10.756.el6.x86_64.rpm

# centos7.5的防火墙

- 样例
  添加
  firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）

  重新载入
  firewall-cmd --reload

  查看
  firewall-cmd --zone=public --list-ports

  删除
  firewall-cmd --zone= public --remove-port=80/tcp --permanent

- firewalld的基本使用
  启动：  systemctl start firewalld
  查状态：systemctl status firewalld
  停止：  systemctl disable firewalld
  禁用：  systemctl stop firewalld
  在开机时启用一个服务：systemctl enable firewalld.service
  在开机时禁用一个服务：systemctl disable firewalld.service
  查看服务是否开机启动：systemctl is-enabled firewalld.service
  查看已启动的服务列表：systemctl list-unit-files|grep enabled
  查看启动失败的服务列表：systemctl --failed

- 配置firewalld-cmd
  查看版本： firewall-cmd --version
  查看帮助： firewall-cmd --help
  显示状态： firewall-cmd --state
  查看所有打开的端口： firewall-cmd --zone=public --list-ports
  更新防火墙规则： firewall-cmd --reload
  查看区域信息:  firewall-cmd --get-active-zones
  查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
  拒绝所有包：firewall-cmd --panic-on
  取消拒绝状态： firewall-cmd --panic-off
  查看是否拒绝： firewall-cmd --query-panic

- 那怎么开启一个端口呢
  添加
  firewall-cmd --zone=public(作用域) --add-port=80/tcp(端口和访问类型) --permanent(永久生效)
  firewall-cmd --zone=public --add-service=http --permanent
  firewall-cmd --reload    # 重新载入，更新防火墙规则
  firewall-cmd --zone= public --query-port=80/tcp  #查看
  firewall-cmd --zone= public --remove-port=80/tcp --permanent  # 删除

  firewall-cmd --list-services
  firewall-cmd --get-services
  firewall-cmd --add-service=<service>
  firewall-cmd --delete-service=<service>
  在每次修改端口和服务后/etc/firewalld/zones/public.xml文件就会被修改,所以也可以在文件中之间修改,然后重新加载
  使用命令实际也是在修改文件，需要重新加载才能生效。

  firewall-cmd --zone=public --query-port=80/tcp
  firewall-cmd --zone=public --query-port=8080/tcp
  firewall-cmd --zone=public --query-port=3306/tcp
  firewall-cmd --zone=public --add-port=8080/tcp --permanent
  firewall-cmd --zone=public --add-port=3306/tcp --permanent
  firewall-cmd --zone=public --query-port=3306/tcp
  firewall-cmd --zone=public --query-port=8080/tcp
  firewall-cmd --reload  # 重新加载后才能生效
  firewall-cmd --zone=public --query-port=3306/tcp
  firewall-cmd --zone=public --query-port=8080/tcp

- 参数解释
  –add-service #添加的服务
  –zone #作用域
  –add-port=80/tcp #添加端口，格式为：端口/通讯协议
  –permanent #永久生效，没有此参数重启后失效

- 详细使用
  firewall-cmd --permanent --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.0.4/24" service name="http" accept'    //设置某个ip访问某个服务
  firewall-cmd --permanent --zone=public --remove-rich-rule='rule family="ipv4" source address="192.168.0.4/24" service name="http" accept' //删除配置
  firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=192.168.0.1/2 port port=80 protocol=tcp accept'     //设置某个ip访问某个端口
  firewall-cmd --permanent --remove-rich-rule 'rule family=ipv4 source address=192.168.0.1/2 port port=80 protocol=tcp accept'     //删除配置

  firewall-cmd --query-masquerade  # 检查是否允许伪装IP
  firewall-cmd --add-masquerade    # 允许防火墙伪装IP
  firewall-cmd --remove-masquerade # 禁止防火墙伪装IP

  firewall-cmd --add-forward-port=port=80:proto=tcp:toport=8080   # 将80端口的流量转发至8080
  firewall-cmd --add-forward-port=proto=80:proto=tcp:toaddr=192.168.1.0.1 # 将80端口的流量转发至192.168.0.1
  firewall-cmd --add-forward-port=proto=80:proto=tcp:toaddr=192.168.0.1:toport=8080 # 将80端口的流量转发至192.168.0.1的8080端口
