# Minio

集群版条件
> [!NOTE]
> * 单独无数据磁盘使用 
> * minio节点的时间不能超过3s

## nginx 配置
```text
upstream xxx.com {
   least_conn;
   server 127.0.0.1:9000;
}
upstream xxx.com-console {
   least_conn;
   server 127.0.0.1:9001;
}

server {
   listen       80;
   listen  [::]:80;
   server_name  xxx.com;

   access_log  /var/log/nginx/xxx.com.access.log  main;
   error_log   /var/log/nginx/xxx.com.error.log warn;
   # Allow special characters in headers
   ignore_invalid_headers off;
   # Allow any size file to be uploaded.
   # Set to a value such as 1000m; to restrict file size to a specific value
   client_max_body_size 0;
   # Disable buffering
   proxy_buffering off;
   proxy_request_buffering off;

   location / {
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_connect_timeout 300;
      # Default is HTTP/1, keepalive is only enabled in HTTP/1.1
      proxy_http_version 1.1;
      proxy_set_header Connection "";
      chunked_transfer_encoding off;

      proxy_pass <http://xxx.com>; # This uses the upstream directive definition to load balance
   }

   location /minio {
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-NginX-Proxy true;

      # This is necessary to pass the correct IP to be hashed
      # real_ip_header X-Real-IP;

      proxy_connect_timeout 300;

      # To support websockets in MinIO versions released after January 2023
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";

      chunked_transfer_encoding off;

      proxy_pass <http://xxx.com-console>; # This uses the upstream directive definition to load balance and assumes a static Console port of 9001
   }
}
```
## minio 配置

+ useradd minio -s /sbin/nologin
+ /etc/default/minio
```bash
MINIO_VOLUMES="<http://192.168.1.141/data/minio> <http://192.168.1.140/data/minio>"
MINIO_OPTS="--console-address :9001 --address :9000"
MINIO_ROOT_USER=admin
MINIO_ROOT_PASSWORD=xxx
# MINIO_SERVER_URL="<https://minio.example.net:9000>"
```
+ /usr/lib/systemd/system/minio.service
```text
[Unit]
Description=MinIO
Documentation=https://min.io/docs/minio/linux/index.html
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/usr/local/bin/minio

[Service]
WorkingDirectory=/usr/local

User=minio
Group=minio
ProtectProc=invisible

EnvironmentFile=-/etc/default/minio
ExecStartPre=/bin/bash -c "if [ -z \\"${MINIO_VOLUMES}\\" ]; then echo \\"Variable MINIO_VOLUMES not set in /etc/default/minio\\"; exit 1; fi"
ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES

# MinIO RELEASE.2023-05-04T21-44-30Z adds support for Type=notify (<https://www.freedesktop.org/software/systemd/man/systemd.service.html#Type=>)
# This may improve systemctl setups where other services use `After=minio.server`
# Uncomment the line to enable the functionality
# Type=notify

# Let systemd restart this service always
Restart=always

# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65536

# Specifies the maximum number of threads this process can create
TasksMax=infinity

# Disable timeout logic and wait until process is stopped
TimeoutStopSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target

# Built for ${project.name}-${project.version} (${project.name})
```
+ 开启自启动
```shell
# 创建minio目录（一个盘对应一个目录即可）
mkdir /data/minio
chown -R minio.minio /data/minio
systemctl enable minio
systemctl start minio
```



---

> 作者: Leon  
> URL: https://liangml.github.io/posts/minio/  

