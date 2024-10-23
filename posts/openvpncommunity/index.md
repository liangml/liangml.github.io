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
push &#34;dhcp-option DNS 100.100.2.136&#34;
push &#34;dhcp-option DNS 100.100.2.138&#34;

# 内网推送
push &#34;route 172.16.0.0 255.255.0.0&#34;
# 阿里内网推送
push &#34;route 100.100.0.0 255.255.0.0&#34;
push &#34;route 100.103.0.0 255.255.0.0&#34;
push &#34;route 47.106.223.210 255.255.255.255&#34;

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
> URL: https://liangml.github.io/posts/openvpncommunity/  

