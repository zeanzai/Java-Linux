
# 在Nginx上面配置Https

## 安装Nginx

安装Nginx，可以<a href="/post/dev/install/install-nginx" target="_blank">参考</a>。特别注意安装的时候要把https的支持模块安装上去。
也就是下面的内容：
```
> --with-http_stub_status_module \
> --with-http_gzip_static_module \
> --with-http_ssl_module
```

## 配置
1. 在相关网络服务商平台上面申请ssl证书，并按照要求操作文件
2. 将ssl证书上传到Nginx的相关位置
3. 在Nginx的配置文件中添加下面内容

```shell
server {
    listen       80;
    server_name  abc.google.com;

    location / {
        rewrite ^(.*) https://abc.google.com/$1 permanent;  # 表示把指向http的请求转发到https上
    }
}
server {
    listen       443 ssl;
    server_name  abc.google.com;

    ssl_certificate      /xxx/xxx/nginx/conf/cert/abc/abc.google.com_ca.crt;  # 第二步ssl证书上传的位置
    ssl_certificate_key  /xxx/xxx/nginx/conf/cert/abc/abc.google.com.key;     # 第二步ssl证书上传的位置

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers ALL:!DH:!EXPORT:!RC4:+HIGH:+MEDIUM:!LOW:!aNULL:!eNULL;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers  on;

    location / {
        root   html/abc;  # 网站存放位置
        index  index.html index.htm;
    }
}
```

## 问题
1. 需要开放443、80端口
2. ssl证书需要按网络服务商要求修改
