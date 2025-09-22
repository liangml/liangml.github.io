# Supervisor

+ /etc/supervisor.conf
```bash
[unix_http_server]
file=/tmp/supervisor.sock
[supervisord]
minfds=65535 # supervisord 加大应用的可用链接数
minprocs=65535
user=deploy
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock
```



+ supervisor exsample



```bash
[program:SERVICENAME]
;脚本目录
directory= /data/%(program_name)s/
;脚本执行命令
command=/usr/bin/uwsgi --ini %(program_name)s.ini --ignore-sigpipe
;supervisor启动的时候是否随着同时启动,默认True
autostart=true
;当程序exit的时候，这个program不会自动重启.
autorestart=true
;这个选项是子进程启动多少秒之后，此时状态如果是running，则我们认为启动成功了。默认值为1
startsecs=5
;脚本运行的用户身份
user = USER
;日志输出
stdout_logfile= /var/log/supervisor/%(program_name)s.log
;把stderr重定向到stdout，默认 false
redirect_stderr = true
;stdout日志文件大小，默认 50MB
stdout_logfile_maxbytes = 20MB
;stdout日志文件备份数
stdout_logfile_backups = 20
;environment=
```

---

> 作者: Leon  
> URL: https://liangml.github.io/posts/supervisor/  

