# Letsencrypt

## ssl
```bash
# acme.sh 
# 默认是zerossl，存在认证问题：get authz objec with invalid status, please try again later.
# https://github.com/acmesh-official/acme.sh/wiki/Server
acme.sh --set-default-ca --server letsencrypt
# 申明dns的aksk
export DP_Id="xxx";export DP_Key="xxx"
# 生成需要的泛域名证书
acme.sh --dnssleep --force --issue -k 2048 --dns dns_dp -d '*.exsample.com' -d exsample.com
# 生成nginx 证书
acme.sh --force --install-cert -d *.xxx.net \
--key-file /usr/local/openresty/nginx/conf/ssl/xxx.net.key \
--fullchain-file /usr/local/openresty/nginx/conf/ssl/xxx.net.pem \
--reloadcmd "nginx -s reload"
# 删除证书
acme.sh --remove -d *.exsample.com
# 查看证书信息
acme.sh --info -d *.exsample.com
# 查看证书列表
acme.sh --list
# 设置自动更新
acme.sh --upgrade --auto-upgrade
```
## certbot
```bash
certbot certonly --manual  --preferred-challenges dns
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/letsencrypt/  

