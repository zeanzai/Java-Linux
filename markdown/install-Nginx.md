# 前言

nginx主要用于web服务容器、负载均衡器、反向代理器。

# 信息统计

- 下载地址：http://nginx.org/en/download.html
- 软件版本：nginx-1.14.1
- 安装地址：/usr/setup/nginx-1.14.1
- 配置文件地址：/usr/setup/nginx-1.14.1/conf/nginx.conf
- 占用端口：详见配置文件

# 安装

- 安装依赖及工具

```shell
yum install -y gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

- 创建安装目录

```shell
mkdir /usr/setup/nginx-1.14.1
mkdir /usr/setup/nginx-1.14.1/temp
```

- 解压

```shell
tar zxf nginx-1.14.1.tar.gz
```

- 配置

```shell
cd nginx-1.14.1
./configure --prefix=/usr/setup/nginx-1.14.1 \
--with-http_stub_status_module \
--with-http_gzip_static_module \
--with-http_ssl_module \
--pid-path=/usr/setup/nginx-1.14.1/nginx.pid \
--lock-path=/usr/setup/nginx-1.14.1/lock/nginx.lock \
--error-log-path=/usr/setup/nginx-1.14.1/log/error.log \
--http-log-path=/usr/setup/nginx-1.14.1/log/access.log \
--http-client-body-temp-path=/usr/setup/nginx-1.14.1/temp/client \
--http-proxy-temp-path=/usr/setup/nginx-1.14.1/temp/proxy \
--http-fastcgi-temp-path=/usr/setup/nginx-1.14.1/temp/fastcgi \
--http-uwsgi-temp-path=/usr/setup/nginx-1.14.1/temp/uwsgi \
--http-scgi-temp-path=/usr/setup/nginx-1.14.1/temp/scgi

// 输出
Configuration summary
+ using system PCRE library
+ using system OpenSSL library
+ using system zlib library

nginx path prefix: "/usr/setup/nginx-1.14.1"
nginx binary file: "/usr/setup/nginx-1.14.1/sbin/nginx"
nginx modules path: "/usr/setup/nginx-1.14.1/modules"
nginx configuration prefix: "/usr/setup/nginx-1.14.1/conf"
nginx configuration file: "/usr/setup/nginx-1.14.1/conf/nginx.conf"
nginx pid file: "/usr/setup/nginx-1.14.1/nginx.pid"
nginx error log file: "/usr/setup/nginx-1.14.1/log/error.log"
nginx http access log file: "/usr/setup/nginx-1.14.1/log/access.log"
nginx http client request body temporary files: "/usr/setup/nginx-1.14.1/temp/client"
nginx http proxy temporary files: "/usr/setup/nginx-1.14.1/temp/proxy"
nginx http fastcgi temporary files: "/usr/setup/nginx-1.14.1/temp/fastcgi"
nginx http uwsgi temporary files: "/usr/setup/nginx-1.14.1/temp/uwsgi"
nginx http scgi temporary files: "/usr/setup/nginx-1.14.1/temp/scgi"
```

- 安装

```shell
make
make install
```

- 启动并测试

```shell
cd /usr/setup/nginx-1.14.1/sbin
./nginx
ps aux | grep nginx
firewall-cmd --zone=public --permanent --add-port=80/tcp
firewall-cmd --reload
```

- 配置自启动

```shell
vi /etc/systemd/system/nginx.service

[Unit]
Description=nginx
After=network.target

[Service]
Type=forking
ExecStart=/usr/setup/nginx-1.14.1/sbin/nginx
ExecReload=/usr/setup/nginx-1.14.1/sbin/nginx -s reload
ExecStop=/usr/setup/nginx-1.14.1/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target

systemctl enable nginx.service
systemctl daemon-reload
```
