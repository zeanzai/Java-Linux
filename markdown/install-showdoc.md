# 前言
showdoc是一款基于markdown的api接口共享文档服务系统。
# 信息统计
（主要介绍：下载地址、软件版本、安装地址、配置文档地址、日志文档地址、占用端口、使用地址、用户信息、测试安装结果、其他有用信息等）
下载地址：github（属于开源软件）
软件版本：直接安装最新的即可
安装地址：nginx的html中，使用nginx作为web容器
端口：8100

# 安装步骤

## 安装依赖
```
yum install -y php php-gd php-fpm php-mcrypt php-mbstring php-mysql php-pdo
```
## 创建文件夹
```
mkdir /usr/setup/nginx-1.14.1/html/showdoc
```

## 上传
将show源码上传到/usr/setup/nginx-1.14.1/html/showdoc

## 配置Nginx
```
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
            fastcgi_param  SCRIPT_FILENAME      $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
        location ~ /\.ht {
            deny  all;
        }
    }
```

## 重新加载配置文件
```
systemctl reload nginx
```

## 开放端口
```
firewall-cmd --zone=public --permanent --add-port=8100/tcp
firewall-cmd --reload
```

## 开启php-fpm组件
```
systemctl reload nginx
systemctl enable php-fpm
systemctl restart php-fpm
```

## 执行安装过程
```
浏览器中输入： http://192.168.100.200:8100/install
```

## 解决问题
1. **问题显示**：
`目录 [ ./server/Application/Runtime/ ] 不可写！`
**解决办法**：
`chmod 777 -R ./showdoc/`

2. **问题显示**：
> 请赋予 install 目录以可写权限！ 请赋予 Public/Uploads/ 目录以可写权限！ 请赋予
> server/Application/Runtime 目录以可写权限！ 请赋予
> server/Application/Common/Conf/config.php文件以可写权限！ 请赋予
> server/Application/Home/Conf/config.php 文件以可写权限！
> (如果你确定赋予了文件权限但却一直看到此信息，则可考虑关闭selinux试试)

    **解决方式**：
    关闭selinux，`vi  /etc/selinux/config`，将SELINUX=enforcing改为SELINUX=disabled，保存并重启。

## 删除安装目录
```
rm -rf /usr/setup/nginx-1.14.1/html/showdoc/install
```

## 重载Nginx配置文件
```
systemctl reload nginx
```
