# https配置

## 环境

腾讯云主机：centos7

西部数码域名和证书

## 场景

部署小程序后端，并配置小程序的HTTPS域名。

## 步骤

### 申请ssl证书

创建ssl的证书，创建完成之后付款、补全认证资料，在域名验证类型处选择“文件验证”；

### 验证主机

根据 西部数码 证书列表界面上要求的方式进行配置，验证主机属于域名所有者。

#### 配置nginx的配置文件

```
$ vi /usr/setup/nginx-1.14.1/conf/vhost/test.domainname.com.conf
server {
    listen       80;
    server_name  test.domainname.com;
    location / {
            root   html/test;
            index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

#### 创建文件夹

```
$ mkdir -p /usr/setup/nginx-1.14.1/html/test/.well-known/pki-validation
```

#### 创建校验文件

```
$ vi /usr/setup/nginx-1.14.1/html/test/.well-known/pki-validation/fileauth.txt
```

#### 重启nginx

```
$ systemctl reload nginx
```

初次部署，需要开放 80 端口和 443 端口。

#### 等待

等待十分钟到二十分钟后，证书状态由 审核 状态改为 完成 状态。

#### 配置域名解析

配置一个A记录类型的域名解析。


#### 访问测试

访问：`http://test.domainname.com/.well-known/pki-validation/fileauth.txt`

在浏览器中输入 `test.domainname.com` 测试是否解析成功。

### 配置https

#### 创建证书文件夹

```
$ mkdir /usr/setup/nginx-1.14.1/conf/cert/test
```

#### 下载并合并证书

- 下载证书并解压

找到证书列表对应的证书，点击下载，下载完成之后通过密码解压

- 合并证书

解压之后，需将 .cer 文件中的内容复制到 .crt 文件头部，中间不要有空行

- 上传证书

将 .key 、 .cer 、 .crt 三个文件复制到上一步骤创建的目录中。

#### 修改配置文件

将上面创建的 test.domainname.conf 的文件修改为下面的内容，修改完成后重启 nginx 。

```
$ vi /usr/setup/nginx-1.14.1/conf/vhost/test.domainname.com.conf

server {
    listen       80;
    server_name  test.domainname.com;

    location / {
        rewrite ^(.*) https://test.domainname.com/$1 permanent;
    }
}
server {
    listen       443 ssl;
    server_name  test.domainname.com;

    ssl_certificate      /usr/setup/nginx-1.14.1/conf/cert/test/test.domainname.com_ca.crt;
    ssl_certificate_key  /usr/setup/nginx-1.14.1/conf/cert/test/test.domainname.com.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers ALL:!DH:!EXPORT:!RC4:+HIGH:+MEDIUM:!LOW:!aNULL:!eNULL;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers  on;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real_IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

$ systemctl reload nginx
```

#### 删除验证文件

```
$ rm -rf test/
```

#### 测试

浏览器中输入 `test.domainname.com` 查看是否自动跳转到https。成功。
