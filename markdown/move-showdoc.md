# 旧机迁移新机

- 新机上安装php

```shell
yum install -y php php-gd php-fpm php-mcrypt php-mbstring php-mysql php-pdo
```

出现缺少libmysqlclient.so的问题：

```shell
rpm -ivh Percona-XtraDB-Cluster-shared-55-5.5.37-25.10.756.el6.x86_64.rpm
```

- 旧机备份

将showdoc拷贝

- 配置Nginx

```shell
mkdir showdoc

vi nginx.conf

server {
    listen       8100;
    server_name  127.0.0.1;
    root         html/showdoc;
    index index.php index.html
    error_page  404              /404.html;
    location = /40x.html {

    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {

    }
    location ~ \.php$ {
        root           html/showdoc;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    location ~ /\.ht {
        deny  all;
    }
}
```

- 将备份文件上传到showdoc上面

- 开放端口

```shell
firewall-cmd --zone=public --permanent --add-port=8100/tcp
firewall-cmd --reload
```

- 重启Nginx和php-fpm

```shell
systemctl reload nginx
systemctl enable php-fpm
systemctl restart php-fpm
```

- 浏览器输入

```shell
http://10.168.1.200:8100/
```

- 报 ./Application/Runtime/Cache/Home/2a94b8bda04ff446dcedd42276fc99ee.php 错误

```shell
rm -rf /usr/setup/nginx-1.14.1/html/showdoc/Application/Runtime
chmod 777 -R showdoc
setenforce 0
```

# 安装新版本

- 修改Nginx配置文件

```shell
server {
    listen       8103;
    server_name  127.0.0.1;
    root         html/newshowdoc;
    index index.php index.html
    error_page  404              /404.html;
    location = /40x.html {

    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {

    }
    location ~ \.php$ {
        root           html/newshowdoc;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    location ~ /\.ht {
        deny  all;
    }
}
```

- 开放端口

```shell
firewall-cmd --zone=public --permanent --add-port=8103/tcp
firewall-cmd --zone=public --permanent --remove-port=8103/tcp
firewall-cmd --reload
```

- 上传文件
