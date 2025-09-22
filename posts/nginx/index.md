# Nginx


## trust proxy ip

```text
set_real_ip_from   172.16.0.0/16;
real_ip_header     X-Forwarded-For;
real_ip_recursive on;
```

## 请求方法限制

```text
if ($request_method !~ ^(GET|HEAD|POST)$ ) {
    return 501;
}
```

## try_files

```text
location / {
    try_files $uri $uri/ /index.html;
}
```

---

+ next.js

```text
location / {
    try_files $uri $uri.html $uri/ /index.html;
  }
```

## 443 force ssl

```text
if ($ssl_protocol = "") { return 302 https://$host$request_uri; }
# 302
if ($server_port !~ 443) { rewrite ^(.*)$ https://$host$1 redirect; }
# 301
if ($server_port !~ 443) { rewrite ^(.*)$ https://$host$1 permanent; }
```

## 499

```text
proxy_ignore_client_abort on;
# 确定在客户端关闭连接时是否应关闭与代理服务器的连接，而不在等待响应
proxy_read_timeout 600;
proxy_send_timeout 600;
# 如果超时(默认60s)，Nginx 会主动断开连接，记录504
```

## log format

```text
log_format main  escape=json '{ "time_local": "$time_local", '
                        '"remote_user": "$remote_user", '
                        '"remote_addr": "$remote_addr", '
                        '"http_referer": "$http_referer", '
                        '"request": "$request", '
                        '"method": "$request_method", '
                        '"url_path": "$request_uri", '
                        '"request_body": "$request_body", '
                        '"status": $status, '
                        '"level": "$level",'
                        '"body_bytes_sent": $body_bytes_sent, '
                        '"http_user_agent": "$http_user_agent", '
                        '"http_host": "$http_host", '
                        '"http_requestid": "$http_requestid", '
                        '"http_authorization": "$http_authorization", '
                        '"business": "ngx_access-$host", '
                        '"http_x_forwarded_for": "$http_x_forwarded_for", '
                        '"upstream_addr": "$upstream_addr",'
                        '"trace_id": "$trace_id",'
                        '"upstream_response_time": "$upstream_response_timer",'
                        '"ssl_protocol": "$ssl_protocol",'
                        '"request_time": $request_time'
                        ' }';
```

## 跨域

```text
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Methods *;
add_header Access-Control-Allow-Credentials true;
```

## 上传文件

```text
# nginx
client_max_body_size 1024m;
```

```text
# php
file_uploads on        是否允许通过HTTP上传文件的开关。
默认为ON即是开upload_tmp_dir – 文件上传至服务器上存储临时文件的地方，如果没指定就会用系统默认的临时文件夹
upload_max_filesize 8m 望文生意，即允许上传文件大小的最大值。默认为2M
post_max_size 8m       指通过表单POST给PHP的所能接收的最大值，包括表单里的所有值。默认为8M
# 针对网络不好配置
max_execution_time 600  每个PHP页面运行的最大时间值(秒)，默认30秒
max_input_time 600      每个PHP页面接收数据所需的最大时间，默认60秒
memory_limit 8m         每个PHP页面所吃掉的最大内存，默认8M
```

## proxy

```plain
location ~ .*\.(js|css)?$ {
	expires 12h;
	proxy_pass http://xxx;
}
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)?$ {
	expires 12h;
	proxy_pass http://xxx;
}
```

## exsample

### http

```text
upstream xxx.cn {
	server  10.x:3000  weight=10 max_fails=3 fail_timeout=3s;
	server  10.x:3000  weight=10 max_fails=3 fail_timeout=3s;

 	check interval=1000 rise=2 fall=3 timeout=5000 type=http default_down=false;
 	check_http_send "GET /ping HTTP/1.0\\r\\n\\r\\n";
 	check_http_expect_alive http_2xx http_3xx;
}

server {
	listen	80;
	server_name  xxx.cn;
	index index.html index.htm;
	access_log  /var/log/nginx/xxx.cn.access.log  main;
	error_log   /var/log/nginx/xxx.cn.error.log warn;

	location ~ ^/NginxStatus/ {
			stub_status on;
			access_log on;
		}
	location / {
		proxy_redirect off ;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header REMOTE-HOST $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_connect_timeout 600;
		proxy_send_timeout 600;
		proxy_read_timeout 600;
		proxy_ignore_client_abort on;
		proxy_buffer_size 1600k;
		proxy_buffers 4 3200k;
		proxy_busy_buffers_size 6400k;
		proxy_temp_file_write_size 6400k;
		proxy_max_temp_file_size 128m;
		proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
		proxy_pass	http://xxx.cn;
	}
}
```

### php

```text
listen 80;
listen [::]:80;
listen 443 ssl http2;
listen [::]:443 ssl http2;
ssl_certificate /usr/local/openresty/nginx/conf/ssl/xxx.com.pem;
ssl_certificate_key /usr/local/openresty/nginx/conf/ssl/xxx.com.key;
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ecdh_curve X25519:prime256v1:secp384r1:secp521r1;
ssl_ciphers ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256;
ssl_conf_command Ciphersuites TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_128_GCM_SHA256;
ssl_conf_command Options PrioritizeChaCha;
ssl_prefer_server_ciphers on;
ssl_session_timeout 10m;
ssl_session_cache shared:SSL:10m;
ssl_buffer_size 2k;
add_header Strict-Transport-Security max-age=15768000;
ssl_stapling on;
ssl_stapling_verify on;
server_name xxx;
access_log /var/log/xxx.log combined;
index index.html index.htm index.php;
root xxx_path;

if ($ssl_protocol = "") { return 302 https://$host$request_uri; }
location ~ [^/]\\.php(/|$) {
  #fastcgi_pass remote_php_ip:9000;
  fastcgi_pass unix:/dev/shm/php-cgi.sock;
  fastcgi_index index.php;
  include fastcgi.conf;
}
location ~ .*\\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
  expires 30d;
  access_log off;
}
location ~ .*\\.(js|css)?$ {
  expires 7d;
  access_log off;
}
location ~ /(\\.user\\.ini|\\.ht|\\.git|\\.svn|\\.project|LICENSE|README\\.md|\\.env) {
  deny all;
}
location /.well-known {
  allow all;
}
```

## default_server

```text
# 手动生成本地ssl公私钥
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt

# 增加default_server
cat << 'EOF' > /etc/nginx/sites-available/000_default
server {
    listen 80 default_server;
    listen 443 ssl default_server;
    ssl_certificate        /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key    /etc/nginx/ssl/nginx.key;
    server_name _;
    return 444;
    access_log /var/log/nginx/000_default.access.log;
    error_log /var/log/nginx/000_default.error.log;
} 
EOF
# reload nginx
nginx -t
nginx -s reload

# 查看日志，检查其他域名是否正常
tailf /var/log/nginx/000_default.access.log
```

## nginx warn variables_hash

> nginx: [warn] could not build optimal variables_hash, you should increase either variables_hash_max_size: 1024 or
> variables_hash_bucket_size: 64; ignoring variables_hash_bucket_size

```shell
# 解决方法,在http中添加
variables_hash_max_size 4096;
variables_hash_bucket_size 2048;
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/nginx/  

