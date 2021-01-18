# nginx

1. 首先利用配置文件启动nginx。

   - 命令:` nginx -c /usr/local/nginx/conf/nginx.conf`

   - 重启服务：` service nginx restart`

2. 快速停止或关闭Nginx：`nginx -s stop`

3. 正常停止或关闭Nginx：`nginx -s quit`

4. 配置文件修改重装载命令：`nginx -s reload`



```nginx
# 配置一个server
server {
	listen <外网port>;
    server_name <name>;
    location <路径/url> {
    	proxy_pass <内网url>;
    }
    ...
}
# 转发80
server {
    listen 80;
    server_name <server_ip>;
    location / {
        proxy_pass <server_ip>;
    }
}

```

### 示例

```
#user www-data;
user ubuntu;
worker_processes auto;
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

    upstream jupyter {
        server 172.17.0.2:8888;
    }
    upstream hugo-simple {
        server localhost:1313;
    }
    #
    ##
    # Basic Settings
    ##
    
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;
    
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;
    
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    
    ##
    # SSL Settings
    ##
    
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;
    
    ##
    # Logging Settings
    ##
    
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    
    ##
    # Gzip Settings
    ##
    
    gzip on;
    gzip_disable "msie6";
    
    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    
    ##
    # Virtual Host Configs
    ##
    
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;


	# ddddd
	server {
		autoindex on;
		autoindex_format html;
		autoindex_exact_size off;
		autoindex_localtime on;
		listen 9090;
		server_name localhost;
	
		charset 'utf-8';
		# 404	
		error_page 404 /404.html;
		location = /404.html {
			root /home/ubuntu/web;
		}
	        location /notebook/ {
	   
			root /home/ubuntu/notebook;
		    # location ~\.md$ {
			    # add_header Content-Type 'text/plain; charset=utf-8';
	        # }
	   		}   
		# 目录
		location / {
			#root /home/ubuntu/web/;
			root /home/ubuntu;
			#proxy_pass http://www.baidu.com;
	
		}
	    # md文件	
		# nignx.conf文件
		location /nginx.conf {
			#root /home/ubuntu/web/;
			root /etc/nginx/;
			add_header Content-Type 'text/plain; charset=utf-8';
			#proxy_pass http://www.baidu.com;
		}
	
		location /l4 {
			root /home/ubuntu/web/l4.html;
		}
	
	}


	# p8080
	## 配置反向代理的参数
	server {
	    listen    8080;
	
	    ## 1. 用户访问 http://ip:port，则反向代理到 https://github.com
		location / {
			proxy_pass  https://github.com;
			proxy_redirect     off;
			proxy_set_header   Host             $host;
			proxy_set_header   X-Real-IP        $remote_addr;
			proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
		}
	
		## 2.用户访问 http://ip:port/README.md，则反向代理到
		##   https://github.com/zibinli/blog/blob/master/README.md
		location /README.md {
			proxy_set_header  X-Real-IP  $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_pass https://github.com/zibinli/blog/blob/master/README.md;
		}
	}
	# p80 
	server {
	    listen    80;
	    server_name 49.234.121.203;
		location /{
	        root /home/ubuntu/web/blog/;
	        index index.html;
			# proxy_pass  http://jupyter;
	        # proxy_set_header    Host             $host;
		}
	
	}
	# hugo 
	server {
	    listen    9999;
	    server_name 49.234.121.203;
		location /{
	        # root /home/ubuntu/web/blog/;
	        # index index.html;
			proxy_pass  http://hugo-simple;
	        # proxy_set_header    Host             $host;
		}
	
	}
	
	server {
		error_page 404 /home/ubuntu/web/404.html;
	}

   # p8077 
    # server {
    #     listen    8077;
    #     server_name 49.234.121.203;
    	# location /{
    #         # root /home/ubuntu/web/blog/;
    #         # index index.html;
    		# proxy_pass  http://0.0.0.0:8077;
    #         # proxy_set_header    Host             $host;
    	# }
    # }

}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
# 
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}


```



# Uwsgi

```sh
# 命令
uwsgi --reload ini
uwsgi --stop pid
uwsgi --ini ini
```



```sh
# 配置文件 ini
[uwsgi]
http-socket = 127.0.0.1:5000

# 项目路径
chdir = <项目路径>
# 启动文件
wsgi-file = manager.py
# 程序内启用的application变量名
callable = app
# 处理器个数
processes = 2
# 线程个数
threads = 4
# 获取uwsgi统计信息的服务地址
stats = 127.0.0.1:5001

daemonize = <log路径及文件名>

pidfile = <pid路径及文件名> # 用于停止等

# 请求大小限制
buffer-size = 65535

# autoreload ?
```

