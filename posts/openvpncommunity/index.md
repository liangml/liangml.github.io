# OpenvpnCommunity

```shell
port 2305
proto udp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh.pem
auth SHA512
tls-crypt tc.key
topology subnet
server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "dhcp-option DNS 100.100.2.136"
push "dhcp-option DNS 100.100.2.138"

# 内网推送
push "route 172.16.0.0 255.255.0.0"
# 阿里内网推送
push "route 100.100.0.0 255.255.0.0"
push "route 100.103.0.0 255.255.0.0"
push "route 47.106.223.210 255.255.255.255"

keepalive 10 120
cipher AES-256-CBC
user nobody
group nobody
persist-key
persist-tun
verb 3
crl-verify crl.pem
explicit-exit-notify
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/openvpncommunity/  

