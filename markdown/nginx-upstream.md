```shell
############################################################################
    # 1. 内容管理系统 start
    upstream manager.taotao.com {
	server 192.168.100.202:8081;
    }
    server {
        listen       80;
        server_name  manager.taotao.com;

        location / {
            proxy_pass   http://manager.taotao.com;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # 内容管理系统 end
    ############################################################################

    ############################################################################
    # 2. 门户服务器 start
    upstream www.taotao.com {
	server 192.168.100.202:8081;
    }
    server {
        listen       80;
        server_name  www.taotao.com;

        location / {
            proxy_pass   http://www.taotao.com;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # 门户服务器 end
    ############################################################################

    ############################################################################
    # 3. rest服务 start
    upstream rest.taotao.com {
	server 192.168.100.202:8081;
    }
    server {
        listen       80;
        server_name  rest.taotao.com;

        location / {
            proxy_pass   http://rest.taotao.com;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # rest服务 end
    ############################################################################

    ############################################################################
    # 4. sso单点登录 start
    upstream sso.taotao.com {
	server 192.168.100.202:8081;
    }
    server {
        listen       80;
        server_name  sso.taotao.com;

        location / {
            proxy_pass   http://sso.taotao.com;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # sso单点登录 end
    ############################################################################

    ############################################################################
    # 5. search服务 start
    upstream search.taotao.com {
	server 192.168.100.202:8081;
    }
    server {
        listen       80;
        server_name  search.taotao.com;

        location / {
            proxy_pass   http://search.taotao.com;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    # search服务 end
    ############################################################################


```
