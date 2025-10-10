# Apisix


# Nginx proxy_pass + / 问题:

```shell
location /api {
	proxy_pass http://api_proxy/;
}
# 访问: http://api.xxx.com/api/admin;
# 转发效果：http://api_proxy/admin;
```

> 由于Apisix 使用的是radixtree 的写法, 导致只支持匹配规则(Full match,Prefix matching) , 并且不会去掉匹配的路径, 这个时候,
> 为了去掉上面的 /api , 需要使用到插件：proxy-rewrite<br>
> Apisix-Router：https://apisix.apache.org/zh/docs/apisix/terminology/router<br>
> Proxy-rewrite：https://apisix.apache.org/zh/docs/apisix/plugins/proxy-rewrite<br>
> ngx_http_rewirte_module：https://nginx.org/en/docs/http/ngx_http_rewrite_module.html<br>
> ngx_http_proxy_module：https://nginx.org/en/docs/http/ngx_http_proxy_module.html<br>
> ngx_stream_proxy_module：http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html

```json
{
  "uri": "/api*",
  "name": "api",
  "id": "489259072256738721",
  "upstream": {
    "type": "roundrobin",
    "pass_host": "pass",
    "nodes": {
      "10.1.1.1:3030": 10
    },
    "timeout": {
      "send": 6,
      "connect": 6,
      "read": 6
    },
    "scheme": "http",
    "keepalive_pool": {
      "idle_timeout": 60,
      "requests": 1000,
      "size": 320
    }
  },
  "plugins": {
    "proxy-rewrite": {
      "regex_uri": [
        "^/api(.*)$",
        "$1"
      ]
    }
  },
  "status": 1,
  "host": "api.xxx.com"
}
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/apisix/  

